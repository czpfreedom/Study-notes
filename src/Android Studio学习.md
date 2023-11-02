# Android Studio学习

## 1. 什么是Android应用

### Android应用运行在哪

​	安卓应用是运行在虚拟机中的。安卓应用是使用Java语言编写的，然后使用Android SDK编译成Dalvik字节码，最后在Dalvik虚拟机中运行。Dalvik虚拟机是Android操作系统的一部分，它是一个基于寄存器的虚拟机，专门为移动设备设计。

#### Dalvik虚拟机

Dalvik虚拟机是Google等厂商合作开发的Android移动设备平台的核心组成部分之一。它可以支持已转换为.dex（即「Dalvik Executable」）格式的Java应用程序的运行。.dex格式是专为Dalvik设计的一种压缩格式，适合内存和处理器速度有限的系统。

Dalvik虚拟机主要负责完成对象生命周期管理、堆栈管理、线程管理、安全和异常管理，以及垃圾回收等。

## 常用数据库

### 1.1.  LitePal

### 1.2.  Sqlite

### 1.3.  Room

Room不是数据库，而是数据库框架。它是GOOGLE推出的，基于SQLLite的框架。

Room最大的优点，就是将Sqlite中，许多繁琐的定义，用一句简单的注释来实现。

Room主要由三部分组成

1. Room Database 数据库：底层连接的主要接入点，创建数据库就靠它了。

2. Data Access Objects DAO：在DAO中会有一系列对数据库进行CRUD的方法声明。（注：CRUD即Create，Read，Update，Delete）

3. Entity 实体类：是对象与数据表的对应表现，设计实体类，并最后转化为对应的数据表。

#### 1.3.1.   引入SDK

在<build.gradle>中：

1. 加入plugins

```
id 'kotlin-kapt'
```

2. 加入dependencies

```
implementation 'androidx.room:room-runtime:2.4.2'
implementation 'androidx.room:room-ktx:2.4.2'
kapt 'androidx.room:room-compiler:2.4.2'
```

#### 1.3.2.   Enity层

```java
@Entity(tableName = "person")
public class Person {

    /**
     * 使用@PrimaryKey声明为主键，并且允许自动生成
     * 使用@ColumnInfo表明这个属性是表中的一列列名，并可以指明列的名称
     */
    @PrimaryKey(autoGenerate = true)
    @ColumnInfo(name = "id")
    private int id;

    @ColumnInfo(name = "first_name")
    private String firstName;

    @ColumnInfo(name = "last_name")
    private String lastName;

    @ColumnInfo(name = "age")
    private int age;

    @ColumnInfo(name = "region")
    private String region;

    //必须指定一个构造方法，room框架需要。并且只能指定一个
    //如果有其他构造方法，则其他的构造方法必须添加@Ignore注解
    @Ignore
    public Person(){

    }

    /**
     * 为了更新数据时使用
     * @param id
     * @param firstName
     */
    public Person(int id, String firstName){
        this.id = id;
        this.firstName = firstName;
    }

    /**
     * 为了删除对象时使用
     * @param id
     */
    @Ignore
    public Person(int id){
        this.id = id;
    }
    /**
     * 为了方便直接构造对象使用
     * @param firstName
     * @param lastName
     * @param age
     * @param region
     */
    @Ignore
    public Person(String firstName, String lastName, int age, String region){
        this.firstName = firstName;
        this.lastName = lastName;
        this.age = age;
        this.region = region;
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getFirstName() {
        return firstName;
    }

    public void setFirstName(String firstName) {
        this.firstName = firstName;
    }

    public String getLastName() {
        return lastName;
    }

    public void setLastName(String lastName) {
        this.lastName = lastName;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public String getRegion() {
        return region;
    }

    public void setRegion(String region) {
        this.region = region;
    }

    @Override
    public String toString() {
        return "Person{" +
                "id=" + id +
                ", firstName='" + firstName + '\'' +
                ", lastName='" + lastName + '\'' +
                ", age=" + age +
                ", region='" + region + '\'' +
                '}';
    }
}
```

#### 1.3.3.   DAO层

```java
/**
 * DAO层接口,需要添加@Dao注解声明
 * 所有的操作都以主键为依托进行
 */
@Dao
public interface PersonDao {

    /**
     * 查询所有的数据，返回List集合
     * @return
     */
    @Query("Select * from person")
    List<Person> getAllPersonList();

    /**
     * 传递参数的集合，注意 Room通过参数名称进行匹配，若不匹配，则编译出现错误
     * @param personId
     * @return
     */
    @Query("select * from person where id in (:personId)")
    List<Person> getPersonById(int[] personId);

    /**
     * 返回一定条件约束下的数据，注意参数在查询语句中的写法
     * @param minAge
     * @param maxAge
     * @return
     */
    @Query("select * from person where age between :minAge and :maxAge")
    List<Person> getPersonByChosen(int minAge, int maxAge);

    /**
     * 插入数据，onConflict = OnConflictStrategy.REPLACE表明若存在主键相同的情况则直接覆盖
     * 返回的long表示的是插入项新的id
     * @param person
     * @return
     */
    @Insert(onConflict = OnConflictStrategy.REPLACE)
    long insertPerson(Person person);

    /**
     * 更新数据，这意味着可以指定id然后传入新的person对象进行更新
     * 返回的long表示更新的行数
     * @param person
     * @return
     */
    @Update
    int updatePerson(Person person);

    /**
     * 删除数据，根据传入实体的主键进行数据的删除。
     * 也可以返回long型数据，表明从数据库中删除的行数
     * @param person
     * @return
     */
    @Delete
    int deletePerson(Person person);
}
```

StudentDao 是一个接口，但是我们不用实现这个Interface，因为Room会自动生成一个实现类：StudentDao_Impl。它自动将StudentDao中的Annotation一一实现。

#### 1.3.4.   DataBase层

```java
@Database(entities = {Person.class}, version = 1)
public abstract class PersonDataBase extends RoomDatabase {
    public abstract PersonDao mPersonDao();
}
```

#### 1.3.5.   在Activity中操作数据库

```java
private PersonDao mPersonDao;
PersonDataBase personDataBase = Room.databaseBuilder(
                getApplicationContext(),
                PersonDataBase.class,
                "personDemo"
        ).build();
mPersonDao = personDataBase.mPersonDao();
```

## 2. build.gradle

在 Android 应用的目录结构中，通常会有两个**build.gradle**文件：一个位于应用目录下，另一个位于项目目录下。这两个文件有着不同的功能和作用。

1. 项目目录下的**build.gradle**文件（也称为项目的**build.gradle**文件）：这个文件定义了整个项目的构建设置，包括多个应用模块、库模块、插件等。
2. 应用目录下的**build.gradle**文件（也称为应用模块的**build.gradle**文件）：这个文件定义了应用模块的构建设置，包括源代码、资源、依赖库等。

## 3. View类

### 3.1.  事件监听

事件监听器是 **View**类中包含一个回调方法的接口。

该回调方法是其相应接口的唯一成员。

如要定义其中一个方法并处理事件，请在 Activity 中实现嵌套接口或将其定义为匿名类。然后，将实现的实例传递给相应的**View.set...Listener()**方法。

```java
// Create an anonymous implementation of OnClickListener
private OnClickListener corkyListener = new OnClickListener() {
    public void onClick(View v) {
      // do something when the button is clicked
    }
};

protected void onCreate(Bundle savedValues) {
    ...
    // Capture our button from layout
    Button button = (Button)findViewById(R.id.corky);
    // Register the onClick listener with the implementation above
    button.setOnClickListener(corkyListener);
    ...
}
```

#### 3.1.1.   onClick()

在**View.OnClickListener**中。当用户轻触项目（在触摸模式下），或者使用导航键或轨迹球聚焦于项目，然后按适用的“Enter”键或按下轨迹球时，系统会调用此方法。

#### 3.1.2.   onLongClick()

在**View.OnLongClickListener**中。当用户轻触并按住项目（在触摸模式下）时，或者使用导航键或轨迹球聚焦于项目，然后按住适用的“Enter”键或按住轨迹球（持续一秒钟）时，系统会调用此方法。

#### 3.1.3.   onFocusChange()

在**View.OnFocusChangeListener**中。当用户使用导航键或轨迹球转到或离开项目时，系统会调用此方法。

#### 3.1.4.   onKey()

在**View.OnKeyListener**中。当用户聚焦于项目并按下或释放设备上的硬件按键时，系统会调用此方法。

#### 3.1.5.   onTouch()

在**View.OnTouchListener**中。当用户执行可视为触摸事件的操作时，包括按下、释放或屏幕上的任何移动手势（在项目边界内），系统会调用此方法。

#### 3.1.6.   onCreateContextMenu()

在**View.OnCreateContextMenuListener**中。当（因用户持续“长按”而）生成上下文菜单时，系统会调用此方法。

## 4. 输入事件处理

### 4.1.  事件监听

详见View类。

### 4.2.  事件处理

https://developer.android.google.cn/guide/topics/ui/ui-events?hl=zh-cn#java

### 4.3.  触摸模式

### 4.4.  处理焦点

## 5. Event

### 5.1.  KeyEvent

KeyEvent是Android系统中处理按键事件的类,通过对KeyEvent进行监听，我们可以对用户在手机屏幕上的按键行为进行响应，包括虚拟按键和物理按键。

#### 5.1.1.   常用键

1、数字键：

KEYCODE_0 - KEYCODE_9

2、字母键：

KEYCODE_A - KEYCODE_Z

3、功能键：

KEYCODE_DEL（删除键），KEYCODE_ENTER（回车键），KEYCODE_ESCAPE（ESC键）

4、移动键：

KEYCODE_DPAD_UP（上键），KEYCODE_DPAD_DOWN（下键），KEYCODE_DPAD_LEFT（左键），KEYCODE_DPAD_RIGHT（右键），KEYCODE_DPAD_CENTER（中心键）

5、系统键：

KEYCODE_BACK（返回键），KEYCODE_HOME（Home键），KEYCODE_MENU（菜单键），KEYCODE_VOLUME_UP（音量加键），KEYCODE_VOLUME_DOWN（音量减键）

#### 5.1.2.   KeyEvent事件类型

1、ACTION_DOWN：按下

2、ACTION_UP：放开

3、ACTION_MULTIPLE：多次按下

#### 5.1.3.   自定义按键事件

```java
public static final int KEYCODE_F1 = 112;
public static final int KEYCODE_F2 = 113;
public static final int KEYCODE_F3 = 114;
public static final int KEYCODE_F4 = 115;
...
@Override
public boolean onKeyDown(int keyCode, KeyEvent event) {
    switch (keyCode) {
        case KEYCODE_F1:
            //自定义按键F1
            break;
        case KEYCODE_F2:
            //自定义按键F2
            break;
    }
    return super.onKeyDown(keyCode, event);
}
```

#### 5.1.4.   KeyEvent的使用

1. 监听按键事件

```java
@Override
public boolean onKeyDown(int keyCode, KeyEvent event) {
    switch (keyCode) {
        case KeyEvent.KEYCODE_BACK:
            //按下返回键
            break;
        case KeyEvent.KEYCODE_HOME:
            //按下HOME键
            break;
        case KeyEvent.KEYCODE_VOLUME_UP:
            //按下音量加键
            break;
        case KeyEvent.KEYCODE_VOLUME_DOWN:
            //按下音量减键
            break;
    }
    return super.onKeyDown(keyCode, event);
}
```
2. 模拟按键事件

```java
//模拟点击home键
KeyEvent keyEventDown = new KeyEvent(KeyEvent.ACTION_DOWN, KeyEvent.KEYCODE_HOME);
KeyEvent keyEventUp = new KeyEvent(KeyEvent.ACTION_UP, KeyEvent.KEYCODE_HOME);
getWindow().getDecorView().dispatchKeyEvent(keyEventDown);
getWindow().getDecorView().dispatchKeyEvent(keyEventUp);
```

## Kotlin学习

### 继承

#### Kotlin

Kotlin中，继承是通过冒号（:）符号实现的。

实例：

```kotlin
class ChildClass : ParentClass() {
    // ChildClass code
}
```

这实际上继承了父类的构造函数。

#### Java

Java中，继承是通过extends关键字实现的。	

