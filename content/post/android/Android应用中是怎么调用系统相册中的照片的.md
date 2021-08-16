---
title: "Android应用中是怎么调用系统相册中的照片的"
date: 2021-08-15T10:18:53+08:00
draft: false
tags: ["android"]
categories: ["android"]
---

Android应用中是怎么调用系统相册中的照片的？相信很多没有经验的人对此束手无策，为此本文总结了问题出现的原因和解决方法，通过这篇文章希望你能解决这个问题。

#### 使用步骤 

这里我是通过一个简单的demo来讲解怎么去实现这个功能。首先看布局：

```java
 <Button
  android:id="@+id/button2"
  android:layout_width="wrap_content"
  android:layout_height="wrap_content"
  android:layout_marginTop="5dp"
  android:layout_marginEnd="52dp"
  android:layout_marginRight="52dp"
  android:text="choose"
  app:layout_constraintEnd_toEndOf="parent"
  app:layout_constraintTop_toTopOf="parent" />

 <ImageView
  android:id="@+id/imageView"
  android:layout_width="wrap_content"
  android:layout_height="wrap_content"
  android:layout_marginTop="29dp"
  app:layout_constraintEnd_toEndOf="parent"
  app:layout_constraintStart_toStartOf="parent"
  app:layout_constraintTop_toBottomOf="@+id/button"
  app:srcCompat="@mipmap/ic_launcher_round" />
```

很简单，就是一个按钮和一个imageView。然后接下来让我们想想这个功能怎么去实现：

首先打开相册，那么肯定要通过隐式启动相册activity；然后相册返回一个路径，我们就拿这个路径把路径上对应的照片展示出来。思路挺简单的，让我们写写看：
首先看代码：

```java
 private Uri imageUri;
 private ImageView imageView;

 @Override
 protected void onCreate(Bundle savedInstanceState) {
  super.onCreate(savedInstanceState);
  setContentView(R.layout.activity_main);
  imageView = findViewById(R.id.imageView);
  Button button1 = findViewById(R.id.button2);
  button1.setOnClickListener(new View.OnClickListener() {
   @Override
   public void onClick(View v) {
   //动态申请权限
    if (ContextCompat.checkSelfPermission(MainActivity.this,Manifest.permission
      .WRITE_EXTERNAL_STORAGE)!= PackageManager.PERMISSION_GRANTED){
     ActivityCompat.requestPermissions(MainActivity.this,new String[]{Manifest.permission.WRITE_EXTERNAL_STORAGE},1);
    }else{
    //执行启动相册的方法
     openAlbum();
    }
   }
  });
  }
//获取权限的结果
@Override
 public void onRequestPermissionsResult(int requestCode, @NonNull String[] permissions, @NonNull int[] grantResults) {
  if (requestCode == 1){
   if (grantResults.length>0&&grantResults[0] == PackageManager.PERMISSION_GRANTED) openAlbum();
   else Toast.makeText(MainActivity.this,"你拒绝了",Toast.LENGTH_SHORT).show();
  }
 }

//启动相册的方法
private void openAlbum(){
  Intent intent = new Intent("android.intent.action.GET_CONTENT");
  intent.setType("image/*");
  startActivityForResult(intent,2);
 }
```

这里先初始化控件，然后动态申请权限，因为我们要读取照片肯定是要读取内存的权限，记得在AndroidManifest中要写明权限：

```java
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

获取权限后就打开相册选择。相册对应的action是android.intent.action.GET_CONTENT，setType(“image/*”)这个方法表示把所有照片显示出来，然后开启活动。启动活动选择完照片后就会返回一个intent到onActivityResult方法中，所以接下来的主要工作就是如果获取到返回的路径。

我们知道在安卓4.4以后是不能把文件的真实路径直接给别的应用的，所以返回的uri是经过封装的，所以我们要进行解析取出里面的路径。所以这里我们要进行判断安卓版本来进行不同的逻辑，先看代码：

```java
@Override
 protected void onActivityResult(int requestCode, int resultCode, @Nullable Intent data) {
 if (requestCode == 2){
 //判断安卓版本
    if (resultCode == RESULT_OK&&data!=null){
    if (Build.VERSION.SDK_INT>=19)
    handImage(data);
    else handImageLow(data);
   }
  }
 }

//安卓版本大于4.4的处理方法
@RequiresApi(api = Build.VERSION_CODES.KITKAT)
 private void handImage(Intent data){
  String path =null;
  Uri uri = data.getData();
  //根据不同的uri进行不同的解析
  if (DocumentsContract.isDocumentUri(this,uri)){
   String docId = DocumentsContract.getDocumentId(uri);
   if ("com.android.providers.media.documents".equals(uri.getAuthority())){
    String id = docId.split(":")[1];
    String selection = MediaStore.Images.Media._ID+"="+id;
    path = getImagePath(MediaStore.Images.Media.EXTERNAL_CONTENT_URI,selection);
   }else if("com.android.providers.downloads.documents".equals(uri.getAuthority())){
    Uri contentUri = ContentUris.withAppendedId(Uri.parse("content://downloads/public_downloads"),Long.valueOf(docId));
    path = getImagePath(contentUri,null);
   }
  }else if ("content".equalsIgnoreCase(uri.getScheme())){
   path = getImagePath(uri,null);
  }else if ("file".equalsIgnoreCase(uri.getScheme())){
   path = uri.getPath();
  }
  //展示图片
  displayImage(path);
 }


//安卓小于4.4的处理方法
private void handImageLow(Intent data){
  Uri uri = data.getData();
  String path = getImagePath(uri,null);
  displayImage(path);
 }

//content类型的uri获取图片路径的方法
private String getImagePath(Uri uri,String selection) {
  String path = null;
  Cursor cursor = getContentResolver().query(uri,null,selection,null,null);
  if (cursor!=null){
   if (cursor.moveToFirst()){
    path = cursor.getString(cursor.getColumnIndex(MediaStore.Images.Media.DATA));
   }
   cursor.close();
  }
  return path;
 }

//根据路径展示图片的方法
private void displayImage(String imagePath){
  if (imagePath != null){
   Bitmap bitmap = BitmapFactory.decodeFile(imagePath);
   imageView.setImageBitmap(bitmap);
  }else{
   Toast.makeText(this,"fail to set image",Toast.LENGTH_SHORT).show();
  }
 }
```

上面的代码很多但是不要慌，咱们一个一个来，不难理解的。首先我们知道不同的版本有两个不同的方法来展示图片，就是：handImage和handImageLow。content类型的uri通过getImagePath这个方法来获取真实路径，真实路径通过displayImage这个方法就可以展示出来了。所以主要的工作就是怎么拿到真实路径。现在思路清晰了，让我们一个个来看：

首先来看一下两个工具方法：getImagePath和displayImage。

- getImagePath学过内容提供器会知道这个就是通过内容提供器来获取数据。通过这个uri以及selection获取到一个Cursor对象。Cursor是什么呢？不了解的读者可以查看这篇博客Android中的Cursor。然后通过这个Cursor对象的MediaStore.Images.Media.DATA这个参数就可以获取到真实路径了。
- displayImage这个方法收一个真实路径字符串，直接通过BitmapFactory.decodeFile这个方法获取到Bitmap再显示出来就行了

了解了工具方法后，我们的目的就很明确啦：content类型的uri或者真实路径的String。
首先是版本低于4.4的，因为返回的是真实的uri，也就是content开头的那个，所以直接通过getImagePath获取真实路径再通过displayImage展示即可。

接下来这个可能看起来有点头疼，因为要解析不同类型的Uri。我们一个个来看：

- 第一种是document类型的uri。至于什么是document类型的uri这里就不深入了，只要知道有这种类型的uri，要怎么处理就好了。首先我们要获取一个DocumentId，然后再分两种情况处理：

第一种的是media格式的，然后我们要取出后半截字符串我们才能获取到真正的id，这里就真正的id指的是对应数据库表中的id，用于selection的。MediaStore.Images.Media.EXTERNAL_CONTENT_URI就是这个照片的content类型uri，再把selection放进去即可。
第二种通过ContentUris.withAppendedId这个方法即可获取到content类型的uri，这个方法负责把id和contentUri连接成一个新的Uri。这个方法在这里也不详细讲解。

- 第二种的是content类型的，那不用说直接用就行了
- 第三种的是file类型的，这个就是真实路径了，直接getPath就可以获取到了。

看完上述内容，你们掌握Android应用中是怎么调用系统相册中的照片的的方法了吗？

