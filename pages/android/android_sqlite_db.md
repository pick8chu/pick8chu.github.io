---
title: Android App Database - SQLite 
keywords: android, app, database, db, sqlite
last_updated: March 31, 2020
summary: "Android app has some different kinds of DB that you can use. SQLite is one among those, which is easy to use and simple."
sidebar: mydoc_sidebar
permalink: android_sqlite_db.html
folder: mydoc
comments: true
---

## #. Before we started

Just incase if you are not aware of DB, I'll explain it briefly. DB is shorten term of Database. It is used to manage data. Easily speaking, it contains some data that you don't want your app to forget. For example, let's say your app did little bit of calculation for something. If the data is really important, you want to save it so the app won't forget, or won't have to re-calculate it again. So you store those data in DB, where even the app is closed, data will be still remained. 

There are **four kinds of important procedures(operations)** you want to know. These are often called as '**CRUD**'.

- Create : Create new data into database
- Read : Read existing data from database
- Update : Update existing data of database
- Delete : Delete existing data from database

Data in DB is often seen as table, but this depends on the type of databases. SQLite DB that we are using is simple version of RDB(Relational Database). It would help to imagine the DB to think of it as a table like below;

| id   | name  | age  | phone-number |
| ---- | ----- | ---- | ------------ |
| 1    | John  | 24   | 1111-1111    |
| 2    | Emily | 31   | 2222-2222    |
| 3    | Jane  | 25   | 3333-3333    |
| 4    | Emily | 17   | 4444-4444    |

Now, let's say we have to call Emily. Since there are two Emily, we have to put something to distinguish those two. That is what id is for, and we call it as a **PK(Primary Key)**. **Every PKs has to be unique, it cannot be the same at all**, and this is really important.

If you want to know more about it, please google it. I think that would be all we need for this post.



## 1. Make a Service

Make a kotlin class for database service. I named it as 'DatabaseService'. Also, we will use [SQLiteOpenHelper](https://developer.android.com/reference/android/database/sqlite/SQLiteOpenHelper) By implementing it to a database service to use its functions. 

Parameters of this class are follows;

```java
SQLiteOpenHelper(Context context, String name, SQLiteDatabase.CursorFactory factory, int version)
```

You can set those options as you want.

```kotlin
class DatabaseService(context: Context) : SQLiteOpenHelper(context, "myDbName.db", null, 4) {
	...   
}
```

Here, we just made a helper object to create, read, update and delete a database.



## 2. Create a database

Now we need make a database with columns and their names. We will use SQL statement to create a database.



### a. Define column and their names

I want 4 columns, ID, Name, Lat and Lng. **Important note, '_id' is mandatory, you have to have it in your column list.** Here is the code, left side of vals are just indicators of how I will refer the right side from now on. For example, I will call '_id' as 'ID'.

```kotlin
companion object {
    public val ID: String = "_id"
    public val NAME: String = "NAME"
    public val LAT: String = "LAT"
    public val LNG: String = "LNG"
}
```



### b. Create a database with SQL statement

'OnCreate' of kotlin class is basically like the Main function of Java or C++. It will run as the class is created, like a constructor. So we will need to create a database when it hasn't created one. Here is the code.

```kotlin
val TAG = "SQLite"
val TABLE = "LOCATIONS"

// This is the part with SQL statement. Quite straight forward, 
// if 'LOCATIONS' table is not existed, create one with 
// those columns and types. Primary Key would be set of ID and Name.
// ID will be automatically increased when the data will be created.
val DATABASE_CREATE =
    "CREATE TABLE if not exists " + TABLE + " (" +
            "${ID} integer auto_increment," +
            "${NAME} string," +
            "${LAT} string," +
            "${LNG} string," +
            "PRIMARY KEY(${ID},${NAME})" +
            ")"

override fun onCreate(db: SQLiteDatabase?) {
    Log.d(TAG, "Creating: " + DATABASE_CREATE);
    if (db != null) {
        db.execSQL(DATABASE_CREATE)
    }
}
```

Now, if you start the your app and call your database service, it will make your SQLite database for the app.



## 3. CRUD

SQLiteOpenHelper has readable database and writable database. So whenever you would need to do something with the database, you just need to call one of those as your purpose and use it. 

These are examples of how to do CRUD. **Remember, we set 'ID' as 'auto_incremental' so we won't need a data for 'ID'.**

```kotlin
// create
fun addLocation(name:String, lat:String, lng:String) {
    val values = ContentValues()
    values.put(NAME, name)
    values.put(LAT, lat)
    values.put(LNG, lng)
    getWritableDatabase().insert(TABLE, null, values)
}

// read
// Those nulls are many different conditions for specifying data,
// so you may use it when you need a specific data.
fun getLocations() : Cursor {
    return getReadableDatabase()
    .query(TABLE, arrayOf(ID, NAME, LAT, LNG), null, null, null, null, null);
}

// delete
// ? is where the parameter will go in, which will be _name
fun deleteLocationByName(_name : String) : Int {
    return getWritableDatabase().delete(TABLE,"${NAME} = ?" , arrayOf(_name))
}

```

I didn't make update. I'll leave it to you, so try it 😏





### a. Cursor

**Notice that 'getLocations()' returns \<Cursor\> type.** Cursor here is, simply speaking, a cursor of MS Word. Imagine you don't have a mouse but only keyboard, and you only can see one letter. You will have to move around to see more than one letter. 

But as you can guess, this is kinda exhausting. So, I usually put those data into a list and use it. Here is the code for that. 

```kotlin
// When you first get the cursor, it starts from 0,
// so you have to move to first.
var mCursor = myDbName.getLocations()
mCursor.moveToFirst()
locationList = mutableListOf()

while (!mCursor.isAfterLast()) {
    locationList += Location(
        mCursor.getString(mCursor.getColumnIndex("NAME"))
        , mCursor.getDouble(mCursor.getColumnIndex("LAT"))
        , mCursor.getDouble(mCursor.getColumnIndex("LNG"))
    )
    mCursor.moveToNext()
}

data class Location(
    val name: String,
    val lat: Double,
    val lng: Double,
)
```



## 4. Epilogue

This post won't really need any pictures, only codes. It might be little annoying to read it all(cause that's how I would feel), but I hope you find it helpful. 

Have a great day :)