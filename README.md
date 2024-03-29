# Как создать и подключить DLL для java 

Мой jdk - 19. 
Версия dll x64
 
# Установка компилятора x64 для c++
Устанавливаем MinGW именно версию x64
* Скачиваем файл x86_64-posix-seh по ссылки https://sourceforge.net/projects/mingw-w64/files/ или по прямой ссылки 
https://sourceforge.net/projects/mingw-w64/files/Toolchains%20targetting%20Win64/Personal%20Builds/mingw-builds/8.1.0/threads-posix/seh/x86_64-8.1.0-release-posix-seh-rt_v6-rev0.7z/download
* Извлекаем папку mingw64 в папку C:\Program Files (рекомендую) или в любую другую папку 
* В папке mingw64 находим папку bin и копируем полной путь к папке. Пример C:\Program Files\mingw64\bin
* Теперь нужно добавить этот путь в переменную среды. 
  *  Нажимаем сочетание клавиш WIN+R и вводим systempropertiesadvanced   ![image](https://user-images.githubusercontent.com/114221320/205640536-8477c773-5f0c-4be8-b6fd-3e36e79cd5dd.png)
  *  Окрываем параметры среды 
  *  Находим в системных переменных **Path** и кликаем два раза, надимаем создать и вставляем наш путь 
*  Теперь проверим работоспособность: открываем командную строку и пишем g++. Консоль должна вывести 
```
g++: fatal error: no input files
compilation terminated.
```
## Создаем java код 
* Создаем файл Main.java 
* Пишем код ниже 
```
public class Main {

    // нативный метот, реализуется в dll
    public static native void printInDll();
    static{

        //загружает нашу dll. Файл должен быть в одной директории с файлом Main.java
        System.loadLibrary("Main");
    }
    public static void main(String[] args) {
       printInDll();
    }
}
```
* Кампилируем код с помощью команды в консоли. Обратите внимание на точку после -h, она обязательна.
```
javac -h . Main.java
```
* После этого у нас будет создан файл main.h в той же директории. В ней описаны методы, которые нужно реализовать. У нас только один метод Java_Main_printInDll формат название такой: сначала идет Java, затем название класса прописанного в java, а затем название метода 
```
/* DO NOT EDIT THIS FILE - it is machine generated */
#include <jni.h>
/* Header for class Main */

#ifndef _Included_Main
#define _Included_Main
#ifdef __cplusplus
extern "C" {
#endif
/*
 * Class:     Main
 * Method:    printInDll
 * Signature: ()V
 */
JNIEXPORT void JNICALL Java_Main_printInDll
  (JNIEnv *, jclass);

#ifdef __cplusplus
}
#endif
#endif

```
# Создаем DLL
* Теперь нам нужна реализовать наши методы. Создадим файл Main.cpp и прописываем этот код
```

#include <stdio.h>
#include "Main.h" // закоговочный файл каторый был создан при компиляции java 

#ifdef __cplusplus 
extern "C" {
#endif

    JNIEXPORT void JNICALL Java_Main_printInDll(JNIEnv* a, jclass b) // метод взятый из Main.h
    {
        //реализация функции
        printf("Hello\n");
    }
    

#ifdef __cplusplus
}
#endif
```
* Теперь нужно скомпилировать dll. А вот тут нам как раз потребуется наш ранее установленный компилятор. Прописываем команду ниже.
```
Шаблон:
g++ -m64 -x c++ -Wl,--kill-at -shared -I "@JDK path\include" -I "@JDK path\include\win32" -o @NameDll.dll @NameCpp.cpp
Пример: 
g++ -x c++ -Wl,--kill-at -shared -I "D:\java\jdk-11.0.2\include" -I "D:\java\jdk-11.0.2\include\win32" -o Main.dll Main.cpp
```
  * Особо внимательные заметили, что  в файле Main.h такая строчка #include <jni.h> . Если вы открывали через IDE то она сразу же ругалась на этот файл. Тело в том что библиотеке jni предоставляется нашим jdk в папке include. А для работы этой библиотеки нужна библиотека jni_md которая находится в папке win32. Мы учитываем это при компиляции.
@JDK path -это путь к вашему jdk 
@NameDll – имя dll которое вы указали в java  (System.loadLibrary("Main");) 
@ NameCpp – имя вашего cpp файла 

* При удачной компиляции у вас должен появиться файл .dll 
# Компиляция программы
* Компилируем командой 
```
java Main
```
* Если все сделали правильно, то в консоль выводиться сообщения Hello
