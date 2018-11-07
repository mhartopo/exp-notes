### 07 November 2018

Kali ini kita akan menambahkan native method di openjdk dan akan memanggil fungsi yang ada di VM.
Caranya adalah petama kita menambahkan deklarasi native method dan JNI call nya di module jdk. Kemudian setelah itu kita menambahkan implementasi native method tersebut di module hotspot (virtual machine)

##### Buat native method di module jdk
Pada contoh ini akan ditambahkan native method di kelas Runtime.java
1. Buatlah deklarasi method pada kelas yang akan ditambahkan.
```
public native void someMethod();
```
2. Buka Runtime.c lalu buatlah pemanggilan JNI untuk native method yang ingin dibuat
```
JNIEXPORT jint JNICALL
Java_java_lang_Runtime_someMethod(JNIEnv *env, jobject this)
{
    return JVM_SomeMethod(); //nama method di kode native
}

```
3. Tambahkan JNI call tadi ke mapping file, untuk kasus ini akan ditambhakan di
```
jdk/make/mapfiles/libjava/mapfile-vers
```

##### Tambahkan implementasi di module hotspot
1. Pada share/vm/prims/jvm.h (file header jvm) tambahkan deklarasi method
```
JNIEXPORT void JNICALL
JVM_SomeMethod(void);
```
2. Pada file implementadi header diatas, tambahka logic fungsinya, contoh
```
JVM_ENTRY_NO_ENV(void, JVM_SomeMethod(void))
  JVMWrapper("JVM_SomeMethod");
  // do something here
JVM_END
```
3. Tambahkan jvm wrapper tadi ke dalam mapfile, tiap OS akan berbeda format, ikuti format yang ada.
```
make/aix/makefiles/mapfile-vers-debug
make/aix/makefiles/mapfile-vers-product
make/bsd/makefiles/mapfile-vers-debug
make/bsd/makefiles/mapfile-vers-product
make/linux/makefiles/mapfile-vers-debug
make/linux/makefiles/mapfile-vers-product
make/solaris/makefiles/mapfile-vers
```

