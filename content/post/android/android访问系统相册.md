---
title: "Android访问系统相册"
date: 2021-08-15T10:09:12+08:00
draft: false
tags: ["android"]
categories: ["android"]
---



# 主要代码



```kotlin
class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        button.setOnClickListener { applyPermissionAndOpenAlbum() }
    }

    private fun applyPermissionAndOpenAlbum() {
        SoulPermission.getInstance()
            .checkAndRequestPermissions(
                Permissions.build(
                    Manifest.permission.CAMERA,
                    Manifest.permission.WRITE_EXTERNAL_STORAGE
                ),
                object : CheckRequestPermissionsListener {
                    override fun onAllPermissionOk(allPermissions: Array<out Permission>?) {
                        openAlbum()
                    }

                    override fun onPermissionDenied(refusedPermissions: Array<out Permission>?) {
                    }
                })
    }

    private fun openAlbum() {
        val intent = Intent()
        intent.action = Intent.ACTION_PICK
        intent.type = "image/*"
        startActivityForResult(intent, 2)
    }

    override fun onActivityResult(requestCode: Int, resultCode: Int, data: Intent?) {
        val photoPath: String?
        if (requestCode == 2 && resultCode == RESULT_OK) {
            data?.data?.let {
                photoPath = UriUtil.getRealPathFromUri(this, it)
                Glide.with(this).load(photoPath).into(imageView)
            }
        }
        super.onActivityResult(requestCode, resultCode, data)
    }
}
```

# 

