## 07 November 2018

Kali ini kita akan menambahkan native method di openjdk dan akan memanggil fungsi yang ada di VM.
Caranya adalah petama kita menambahkan deklarasi native method dan JNI call nya di module jdk. Kemudian setelah itu kita menambahkan implementasi native method tersebut di module hotspot (virtual machine)

### Buat native method di module jdk

Pada contoh ini akan ditambahkan native method di kelas Runtime.java
Buatlah deklarasi method pada kelas yang akan ditambahkan.
```java
public native void someMethod();
```
Buka Runtime.c lalu buatlah pemanggilan JNI untuk native method yang ingin dibuat
```java
JNIEXPORT jint JNICALL
Java_java_lang_Runtime_someMethod(JNIEnv *env, jobject this)
{
    return JVM_SomeMethod(); //nama method di kode native
}
```
Tambahkan JNI call tadi ke mapping file, untuk kasus ini akan ditambhakan di
```
jdk/make/mapfiles/libjava/mapfile-vers
```

### Tambahkan implementasi di module hotspot
Pada share/vm/prims/jvm.h (file header jvm) tambahkan deklarasi method
```c++
JNIEXPORT void JNICALL
JVM_SomeMethod(void);
```

Pada file implementadi header diatas, tambahka logic fungsinya, contoh
```c++
JVM_ENTRY_NO_ENV(void, JVM_SomeMethod(void))
  JVMWrapper("JVM_SomeMethod");
  // do something here
JVM_END
```

Tambahkan jvm wrapper tadi ke dalam mapfile, tiap OS akan berbeda format, ikuti format yang ada.
```
make/aix/makefiles/mapfile-vers-debug
make/aix/makefiles/mapfile-vers-product
make/bsd/makefiles/mapfile-vers-debug
make/bsd/makefiles/mapfile-vers-product
make/linux/makefiles/mapfile-vers-debug
make/linux/makefiles/mapfile-vers-product
make/solaris/makefiles/mapfile-vers
```

## 08 November 2018
Paraller scavange garbage collector on Java 8
