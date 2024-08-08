# To-do-List-App
Tools Used:  Android Studio: IDE for app development and testing. SQLite Database: Local data storage solution. Languages Used:  Java: For app logic and functionality. XML: For designing the user interface.
To-Do List app internship task at KaiRiz Cyber Technologies and encryptix.
# To-Do List Application Report

## Overview
The To-Do List Application is an Android-based project designed to help users manage their tasks efficiently. The application provides an intuitive user interface, allowing users to add, view, and manage their tasks seamlessly. Core features include a splash screen, a main activity displaying the list of tasks, and the ability to add new tasks via a floating action button. The app uses modern Android components such as `RecyclerView`, `CardView`, and `FloatingActionButton`, combined with a local SQLite database for data persistence.

## Application Components

### 1. Splash Screen
**File:** `activity_splash.xml`

The splash screen serves as an introductory screen that displays the application name and logo when the app is launched. It enhances user experience by providing a polished start to the app.

**Key Elements:**
- `ConstraintLayout` as the root layout.
- `TextView` to display the application name "To Do App".
- Custom styles and colors for the background and text.

```xml
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="@color/colorPrimaryDark"
    tools:context=".SplashActivity">

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintHorizontal_bias="0.5"
        app:layout_constraintStart_toStartOf="parent"
        android:text="To Do App"
        android:textColor="@color/colorWhite"
        android:textSize="40sp"
        android:textStyle="bold"
        android:drawablePadding="15dp"
        android:drawableRight="@drawable/ic_baseline_done_all_24"
        app:layout_constraintTop_toTopOf="parent" />
</androidx.constraintlayout.widget.ConstraintLayout>
```

### 2. Main Activity
**File:** `activity_main.xml`

The main activity displays the list of tasks. It includes a `RecyclerView` to list tasks dynamically and a `FloatingActionButton` to add new tasks.

**Key Elements:**
- `RelativeLayout` as the root layout.
- `TextView` for the header "To Do Tasks".
- `RecyclerView` to display the list of tasks.
- `FloatingActionButton` for adding new tasks.

```xml
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:id="@+id/textview"
        android:text="To Do Tasks"
        android:textSize="32sp"
        android:textColor="#000"
        android:textStyle="bold"
        android:layout_centerHorizontal="true"
        android:layout_marginTop="10dp"/>
    <androidx.recyclerview.widget.RecyclerView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:id="@+id/recyclerview"
        android:layout_below="@id/textview"
        android:nestedScrollingEnabled="true"/>
    <com.google.android.material.floatingactionbutton.FloatingActionButton
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_alignParentEnd="true"
        android:layout_alignParentBottom="true"
        android:id="@+id/fab"
        android:src="@drawable/ic_baseline_add_24"
        android:layout_margin="30dp"/>
</RelativeLayout>
```

### 3. Task Item Layout
**File:** `task_layout.xml`

Each task is displayed within a `CardView` that contains a `MaterialCheckBox`. This layout makes it easy for users to interact with individual tasks.

**Key Elements:**
- `CardView` as the root element for each task item.
- `RelativeLayout` to hold the `MaterialCheckBox`.
- `MaterialCheckBox` to display the task and allow checking off completed tasks.

```xml
<androidx.cardview.widget.CardView xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    app:cardCornerRadius="8dp"
    app:cardElevation="5dp"
    android:layout_marginHorizontal="16dp"
    android:layout_marginVertical="8dp">

    <RelativeLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:padding="8dp">

        <com.google.android.material.checkbox.MaterialCheckBox
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:buttonTint="@color/colorPrimaryDark"
            android:id="@+id/mcheckbox"
            android:text="This is Task 1"
            android:paddingStart="8dp"/>
    </RelativeLayout>
</androidx.cardview.widget.CardView>
```

## Java Code Implementation

### MainActivity.java

**Functionality:**
- Initializes the main components of the application including `RecyclerView`, `FloatingActionButton`, and `DataBaseHelper`.
- Handles adding new tasks through a dialog.
- Implements swipe-to-delete functionality using `ItemTouchHelper`.
- Updates the task list when a task is added or removed.

**Key Elements:**
- `RecyclerView` to display the list of tasks.
- `FloatingActionButton` to trigger adding a new task.
- `DataBaseHelper` to interact with the SQLite database.
- `ToDoAdapter` to bind data to the `RecyclerView`.
- `OnDialogCloseListner` to handle dialog close events and update the task list.

```java
package com.example.todoapp;

import androidx.appcompat.app.AppCompatActivity;
import androidx.recyclerview.widget.ItemTouchHelper;
import androidx.recyclerview.widget.LinearLayoutManager;
import androidx.recyclerview.widget.RecyclerView;

import android.content.DialogInterface;
import android.os.Bundle;
import android.view.View;

import com.example.todoapp.Adapter.ToDoAdapter;
import com.example.todoapp.Model.ToDoModel;
import com.example.todoapp.Utils.DataBaseHelper;
import com.google.android.material.floatingactionbutton.FloatingActionButton;

import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

public class MainActivity extends AppCompatActivity implements OnDialogCloseListner {

    private RecyclerView mRecyclerview;
    private FloatingActionButton fab;
    private DataBaseHelper myDB;
    private List<ToDoModel> mList;
    private ToDoAdapter adapter;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mRecyclerview = findViewById(R.id.recyclerview);
        fab = findViewById(R.id.fab);
        myDB = new DataBaseHelper(MainActivity.this);
        mList = new ArrayList<>();
        adapter = new ToDoAdapter(myDB , MainActivity.this);

        mRecyclerview.setHasFixedSize(true);
        mRecyclerview.setLayoutManager(new LinearLayoutManager(this));
        mRecyclerview.setAdapter(adapter);

        mList = myDB.getAllTasks();
        Collections.reverse(mList);
        adapter.setTasks(mList);

        fab.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                AddNewTask.newInstance().show(getSupportFragmentManager() , AddNewTask.TAG);
            }
        });
        ItemTouchHelper itemTouchHelper = new ItemTouchHelper(new RecyclerViewTouchHelper(adapter));
        itemTouchHelper.attachToRecyclerView(mRecyclerview);
    }

    @Override
    public void onDialogClose(DialogInterface dialogInterface) {
        mList = myDB.getAllTasks();
        Collections.reverse(mList);
        adapter.setTasks(mList);
        adapter.notifyDataSetChanged();
    }
}
```

## Features
1. **Splash Screen:**
    - Displays app name and logo.
    - Enhances user experience.

2. **Main Activity:**
    - Header displaying "To Do Tasks".
    - Dynamic list of tasks using `RecyclerView`.
    - Add new tasks with a `FloatingActionButton`.

3. **Task Item Layout:**
    - Tasks displayed in `CardView` for better visual appeal.
    - `MaterialCheckBox` to mark tasks as completed.

4. **Task Management:**
    - Add new tasks through a dialog.
    - Swipe-to-delete functionality for removing tasks.
    - Persistent storage using SQLite database via `DataBaseHelper`.

## Technical Implementation
- **RecyclerView:** Efficiently handles and displays a large set of data. It uses an adapter to bind the data to the views.
- **CardView:** Provides a consistent layout for each task with elevation and corner radius for a modern look.
- **MaterialCheckBox:** Part of Material Design components, enhances the UI and UX by providing better interactive elements.
- **SQLite Database:** Used for storing tasks persistently. The `DataBaseHelper` class manages database creation, version management, and CRUD operations.

## Conclusion
The To-Do List Application is designed to be user-friendly and efficient in managing daily tasks. With a clean and intuitive interface, it allows users to add, view, and manage their tasks seamlessly. The use of modern Android components and a reliable SQLite database ensures a smooth and consistent user experience. This application serves as an excellent tool for task management, providing essential features and functionality in a straightforward manner.
