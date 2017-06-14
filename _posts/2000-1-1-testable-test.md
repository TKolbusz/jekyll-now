---
layout: post
title: Testable unstested test
---
Google recently improved their Android library arsenal. They introduced Room Persistence Library, that provided easier to deal with, layer over SQLite. Introduction of Room had in mind that, in Android world Rx code grows in popularity.

<b>TL;DR: In your Room’s table Dao, return Flowable&lt;List&lt;SomeEntity&gt;&gt; that will stream of data of specified Query each time database is updated.</b>

If you were writing some app that uses database, you probably had design problems over database changes and updates. You had to worry about each time database table is updated, pull data over and update your UI. Room simplifies all of that to Rx stream as database listener that streams data, each time database is updated. We’re going to implement it to make our lives easier.

I’m going to use Kotlin, RxJava2 and Room of course.

<b>1. Setting up dependencies in module gradle file.</b> 

When you are reading this, there is probably newer version, so check that out. 
<script src="https://gist.github.com/TKolbusz/8857ba47ea5c9468146d0a4c052d76c2.js"></script>
If you are getting gradle sync errors consider adding this your gradle file.
<script src="https://gist.github.com/TKolbusz/f72be3d985979d8a80d099f0365dff8f.js"></script>

<b>2. Setting up Entities. </b>

Here you can set up list of columns in the table you are going to use in your database. 
<script src="https://gist.github.com/TKolbusz/b51e46fbc94c470bcdf5346731fcaac9.js"></script>

<b>3. Setting up Dao.</b>

Here you can list all database queries that you are going to use for specific table.

Simple @Insert @Update, @Delete or custom @Query annotation will generate all SQLite
boilerplate for you. Note that, Room will not compile if you make a typo, so that’s great!
In my version of Room there is a problem with referencing arguments in Query by name, but you can reference them by index, arg0 arg1 and so on.
<script src="https://gist.github.com/TKolbusz/5e1f0da09917c61bfc47ee478fa90d52.js"></script>
<b>4. Setting up Database class.</b>

All you need to do here is connect all your Dao’s and Entities and that’s it, Room will generate all of the code for you.
<script src="https://gist.github.com/TKolbusz/8f4cff7c7c3cbf7c50bac8881c6e4ca3.js"></script>

<b>5. Usage.</b>

You can create Database object using Application Context, I’m injecting it with Dagger2 in top level component, but you can just create it in Application onCreate() for it to be Singleton.

<script src="https://gist.github.com/TKolbusz/646222863e462409fee4f79495412412.js"></script>
 The next usage step is just subscribing to Flowable stream provided by Room, note that in auto-generated RxRoom class, subscribeOn() is already called with background thread, so now you can just add observeOn(AndroidSchedulers.mainThread()), for your data to be caught in main thread. <script src="https://gist.github.com/TKolbusz/1c29403665b59fb893bd104394ae48a6.js"></script>
Important thing is that, Room will stream empty list if database is empty or if it haven’t found any data with specified query.

<b>6. Why Flowable? </b>

Google Developers had in mind that SQLite databases are updated in much shorter periods of time than Android UI is. Flowable adds option to handle backpressure, in short, when there are too many database updates to handle, only most recent update will be emitted through our reactive stream.

Full example can be seen here: [RxRoom example](https://github.com/TKolbusz/RxRoom-example)
