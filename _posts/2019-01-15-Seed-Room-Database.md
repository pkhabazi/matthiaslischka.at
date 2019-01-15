---
title: Seed Android Room Database with Kotlin
layout: post
tags: [Anroid, Kotlin]
---
When you need to add some inital data to you `room` database you can add a callback to the `databaseBuilder` and override `onCreate`. [This method](https://developer.android.com/reference/android/arch/persistence/room/RoomDatabase.Callback#oncreate) will be executed once after the database and all tables are created.

_AppDatabase.kt_
```kotlin
package at.matthiaslischka.someApp.data

import android.arch.persistence.db.SupportSQLiteDatabase
import android.arch.persistence.room.Database
import android.arch.persistence.room.Room
import android.arch.persistence.room.RoomDatabase
import android.content.Context
import at.matthiaslischka.someApp.utils.ioThread

@Database(entities = [YourEntity::class], version = 1, exportSchema = false)

abstract class AppDatabase : RoomDatabase() {
    abstract fun yourDao(): YourDao

    companion object {
        private var INSTANCE: AppDatabase? = null

        fun getInstance(context: Context): AppDatabase =
            INSTANCE ?: synchronized(this) {
                INSTANCE ?: buildDatabase(context).also { INSTANCE = it }
            }

        private fun buildDatabase(context: Context) =
            Room.databaseBuilder(context.applicationContext, AppDatabase::class.java, "YourDatabase.db")
                .addCallback(seedDatabaseCallback(context))
                .build()

        private fun seedDatabaseCallback(context: Context): Callback {
            return object : Callback() {
                override fun onCreate(db: SupportSQLiteDatabase) {
                    super.onCreate(db)
                    ioThread {
                        var yourDao = getInstance(context)!!.yourDao()
                        yourDao.insert(YourEntity(..))
                        yourDao.insert(YourEntity(..))
                        ..
                    }
                }
            }
        }
    }
}
```

`ioThread` is just a small helper that wraps `Executors.newSingleThreadExecutor`.

_Executors.kt_
```kotlin
package at.matthiaslischka.someApp.utils

import java.util.concurrent.Executors

private val IO_EXECUTOR = Executors.newSingleThreadExecutor()

fun ioThread(f : () -> Unit) {
    IO_EXECUTOR.execute(f)
}
```