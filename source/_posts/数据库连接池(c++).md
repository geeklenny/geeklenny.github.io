---
title: 数据库线程池(c++)
comments: false
date: 2016-12-02 16:48:44
tags:
  - 程序设计
  - c++
  - 数据库连接池
categories:
  - 程序设计
  - 数据库连接池
---

# 一、概述

　　对于每一次的数据库访问，都需要有一个承载该次访问操作的数据库连接。现在的应用系统往往伴随着大量的数据库访问。可想而知，如果频繁的建立和关闭链接，会极大的降低系统的性能。

　　数据库连接池是针对数据库连接的性能优化而产生的。它的核心思想是连接复用，并通过连接池高效并安全的维护连接池内的连接，并按需分配到各个调用者，以减少重复创建和删除连接造成的系统资源浪费。

<!--more-->
## 1.1 源代码链接
[数据库连接池](https://github.com/geeklenny/databaseConnPool)

# 二、源代码讲解
　　希望你已经下载了源代码，这里开始对源代码进行讲解。这一份源代码我并没有做过多的性能测试，只是按照数据库连接池的基本思想和原理提供的一份实现。我希望这篇文章可以覆盖到数据库连接池的核心内容。

## 2.1 连接池只是连接池
　　我们编写连接池的目的是用来维护连接，减少系统资源浪费。所以我们避免将连接池设计和实现成通用的带数据库连接优化的数据库访问类。

```cpp
template<typename T>
class CommonDbConnPool{...}
```
　　我们使用模板类来弱化数据库的具体类型，而更关系如何去维护其链接。对于具体的数据库操作，还是沿用使用的数据库提供的接口去访问。记住，数据库连接池只提供可用的连接，或者无可用连接时的错误提示。

## 2.2　连接的初始化
　　提高数据库链接的初始化，核心的问题是启动多少连接合适。启动太少，不能满足应用的需求；启动太多，对系统资源也是一种浪费。实际上，在什么样配置的服务器上，启动多少个连接合适并没有一个明确的参考数据。它们都需要进行测试，调整到最优的连接数。
　　这里提供一个普遍适用于连接池的方法。它可以尽可能的保证系统资源的合理使用。这个方法是通常提供一个最小连接数和最大连接数。最小连接数为启动时默认启动的链接数目。如果最小连接数在被完全使用的时候，又进入一个新连接，那么连接池将为它新建一个连接。新建连接的前提是，连接总数没有超过最大连接数。否则返回连接池已满类似的错误信息。超出最小连接数的这些连接，会被定期回收释放。
　　初始化的同时，启动监控线程，用于定期检测连接是否正常连接。
```cpp
template<typename T>
int CommonDbConnPool<T>::InitPool( int minConn, int maxConn )
{
    ...
    ...
    for ( int i =0 ; i < minConn; i++ )
    {
        if( (t = createDBConn()) != nullptr )
        {
            if( (bConnectionAvilable(t)) )
            {
                m_poolMap[t] = DB_CONN_FREE;
            }
            else
            {
                releaseDBConn(t);
            }
        }
    ...
    ...
    thread th(&CommonDbConnPool<T>::PoolConnMonitor, this);
    swap(th, m_trdConnMonitor);
｝
```

## 2.3 连接的管理
　　存在与连接池中的连接有三种状态。空闲，占用和临时连接。
```cpp
enum DBCONNSTATS
{
    DB_CONN_FREE,
    DB_CONN_USED,
    DB_CONN_TEMP_USED

};
```

　　最小连接数内的连接，初始化为free，占用时为used。而超过最小链接，并未超过最大连接数的这些连接为临时连接，定期会被回收。当然什么时候回收由你来定，我的是现实，当连接被用户释放回连接池时，直接回收。很多的数据库连接池对于临时连接处理会巧妙的多。比如超过最小链接后，扩容一倍等等。
   **需要强调的一点是，为了保证多线程的安全访问，请记得加锁。**

```cpp
template <typename T>
T* CommonDbConnPool<T>::GetDBConnFromPool()
{
    ...
    lock_guard<mutex> locker(m_mtxPool);
    ...
    for(auto &iter : m_poolMap)
    {
        if(iter.second == DB_CONN_FREE){
            iter.second = DB_CONN_USED;
            t = iter.first;
            break;
        }
    }

    if (t == nullptr){
        if(m_poolMap.size() < m_maxConn)
        {
            if( (t = createDBConn()) != nullptr )
            {
                if( (bConnectionAvilable(t)) )
                {
                    m_poolMap[t] = DB_CONN_TEMP_USED;
                }
                else
                {
                    releaseDBConn(t);
                }
            }
        }


    }

    return t;
}

template <typename T>
void CommonDbConnPool<T>::ReleaseDbConnToPool(T* t)
{
    lock_guard<mutex> locker(m_mtxPool);
    for (auto &iter : m_poolMap)
    {
        if(iter.first == t)
        {
            if(iter.second == DB_CONN_USED)
            {
                iter.second = DB_CONN_FREE;
            } else if (iter.second == DB_CONN_TEMP_USED)
            {
                releaseDBConn(iter.first);
                m_poolMap.erase(iter.first);
            }
        }
    }
}
```

## 2.4 释放连接池
　　释放连接也应该人性化，比如某个应用还在使用连接进行着访问，那我们不应该直接关闭该链接。
　　我这里的处理是，关闭所有free的链接。并返回池内连接的大小。如果返回不为0，那么就相当于通知用户，连接并没有完全被释放，你还需要再次调用。
   别忘了我们还有一个监控线程在运行着呢。别忘了结束线程。
```cpp
template <typename T>
void CommonDbConnPool<T>::PoolConnMonitor(){
    ...
    while(ptrPool->m_bPoolAvailable && ! ptrPool->m_bPoolToDestroy)
    {
        ...
        [&](){
            unique_lock<mutex> lk(ptrPool->m_mtxCv);
            ptrPool->m_Cv.wait_for(lk, chrono::milliseconds(ptrPool->ulTimeForCheckTimeout()));
        }();
    ...
    }
}


template <typename T>
int CommonDbConnPool<T>::DestroyPool(){
    ...
    for( auto &iter : m_poolMap)
    {
        if( iter.second == DB_CONN_FREE)
        {
            releaseDBConn(iter.first);
            m_poolMap.erase(iter.first);
        }
    }
    ...
    ...
    ptrPool->m_bPoolToDestroy = true;
    m_Cv.notify_one();
    m_trdConnMonitor.join();

    ...
    return m_poolMap.size();
}

```

# 三、使用
 　我这里使用了mysqlcppconn的库，如果需要请自行安装下载。
 ```cpp
#include <iostream>
#include "./mysql/mysqlConn.h"

using namespace std;

int main(int argc, char *argv[])
{

    MysqlConn* mysqlpool;
    try{

        do{

            //连接数据库
            mysqlpool = new MysqlConn("localhost", "root", "Passw0rd");

            //创建数据库连接池，并返回大小
            cout << " mysqlpool size : " << mysqlpool->InitPool(10,20) << endl;

            sql::Connection *con;
            sql::Statement* stmt;
            sql::ResultSet* res;

            //获取一个连接
            if((con=mysqlpool->GetDBConnFromPool())==nullptr){
                cout<<"reach the max availd" << endl;
                break;
            }

            //执行语句
            con->setSchema("mysql");
            stmt = con->createStatement();
            res = stmt->executeQuery("select * from user");

            //打印数据库连接池信息
            cout<<(*mysqlpool);

            //释放连接到连接池
            mysqlpool->ReleaseDbConnToPool(con);

            //打印数据库连接池信息
            cout<<(*mysqlpool);
            cin.get();

        } while(false);
        //销毁数据库连接池
        if(mysqlpool->DestroyPool()>0){
            cout<<"There are still some un-release connections in the pool\n";
        }

        delete mysqlpool;

    }catch(sql::SQLException &e){
        std::cout<<e.what();
    }
    return 0;
}
```
