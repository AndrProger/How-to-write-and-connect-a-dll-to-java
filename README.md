# Как создать и подключить DLL для java 

Мой jdk - 19. 
Версия dll x64
 
## Установка компилятора x64 для c++
Устанавливаем MinGW именно версию x64
* Скачиваем файл x86_64-posix-seh по ссылки https://sourceforge.net/projects/mingw-w64/files/ или по прямой ссылки 
https://sourceforge.net/projects/mingw-w64/files/Toolchains%20targetting%20Win64/Personal%20Builds/mingw-builds/8.1.0/threads-posix/seh/x86_64-8.1.0-release-posix-seh-rt_v6-rev0.7z/download
* Извлекаем папку mingw64 в папку C:\Program Files (рекомендую) или в любую другую папку 
* В папке mingw64 находим папку bin и копируем полной путь к папке. Пример C:\Program Files\mingw64\bin
* Теперь нужно добавить этот путь в переменную 
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
        test();
    }
}
```
