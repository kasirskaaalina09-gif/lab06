# Laboratory work III

Данная лабораторная работа посвящена изучению систем автоматизации сборки проекта на примере CMake.

## Цель работы

Изучить основы работы с системой автоматизации сборки CMake, научиться создавать статические библиотеки и подключать их к приложениям.

---

## Tasks

1. Создать публичный репозиторий с названием `lab03` на сервисе GitHub.
2. Ознакомиться со ссылками учебного материала.
3. Выполнить инструкцию учебного материала.
4. Составить отчет.

---

# Tutorial

## Создание и настройка репозитория

```sh
$ export GITHUB_USERNAME=<имя_пользователя>

$ cd ${GITHUB_USERNAME}/workspace

$ pushd .

$ source scripts/activate

$ git clone https://github.com/${GITHUB_USERNAME}/lab02.git projects/lab03

$ cd projects/lab03

$ git remote remove origin

$ git remote add origin https://github.com/${GITHUB_USERNAME}/lab03.git

$ g++ -std=c++11 -I./include -c sources/print.cpp

$ ls print.o
```

Вывод:

```sh
examples  hello_world.cpp  include  print.o  README.md  sources
```

Команда:

```sh
nm print.o | grep print
```

Вывод:

```sh
0000000000000000 T _Z5printRKNSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEEERSo
000000000000002a T _Z5printRKNSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEEERSt14basic_ofstreamIcS2_E
```

Команда:

```sh
$ ar rvs print.a print.o
```

Вывод:

```sh
ar: creating print.a
a - print.o
```

Команда:

```sh
$ file  print.a 
```

Вместо нее я использовала команды:

```sh
$ find . -name "libprint.a"
```

```sh
file ./_build/libprint.a
```

Вывод(Для find):

```sh
./_build/libprint.a
./_install/lib/libprint.a
```

Вывод(для file):

```sh
./_build/libprint.a: current ar archive
```

Команда:

```sh
g++ -std=c++11 -I./include -c examples/example1.cpp
```

Вывод:

```sh
example1.o
```

Команды:

```sh
$ g++ example1.o print.a -o example1
```

```sh
./example1 && echo
```

Вывод:

```sh
hello
```

Команды:

```sh
$ g++ -std=c++11 -I./include -c examples/example2.cpp

nm example2.o
```

Вывод:

```sh
0000000000000000 V DW.ref.__gxx_personality_v0
                 U __gxx_personality_v0
0000000000000000 T main
                 U __stack_chk_fail
                 U _Unwind_Resume
                 U _Z5printRKNSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEEERSt14basic_ofstreamIcS2_E
                 U _ZNSt14basic_ofstreamIcSt11char_traitsIcEEC1EPKcSt13_Ios_Openmode
                 U _ZNSt14basic_ofstreamIcSt11char_traitsIcEED1Ev
0000000000000000 W _ZNSt15__new_allocatorIcED1Ev
0000000000000000 W _ZNSt15__new_allocatorIcED2Ev
0000000000000000 n _ZNSt15__new_allocatorIcED5Ev
                 U _ZNSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEEC1EPKcRKS3_
                 U _ZNSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEED1Ev
                 U _ZSt21ios_base_library_initv
0000000000000000 r _ZStL19piecewise_construct
```

Команды:

```sh
$ g++ example2.o print.a -o example2

$ ./example2
```

```sh
$ cat log.txt && echo
```

Вывод:

```sh
hello
```

Команды:

```sh
$ rm -rf example1.o example2.o print.o
$ rm -rf print.a
$ rm -rf example1 example2
$ rm -rf log.txt
```

```sh
$ cat > CMakeLists.txt <<EOF
cmake_minimum_required(VERSION 3.4)
project(print)
EOF
```

```sh
$ cat >> CMakeLists.txt <<EOF
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
EOF
```

```sh
$ cat >> CMakeLists.txt <<EOF
add_library(print STATIC \${CMAKE_CURRENT_SOURCE_DIR}/sources/print.cpp)
EOF
```

```sh
$ cat >> CMakeLists.txt <<EOF
include_directories(\${CMAKE_CURRENT_SOURCE_DIR}/include)
EOF
```

```sh
$ cat >> CMakeLists.txt <<EOF

add_executable(example1 \${CMAKE_CURRENT_SOURCE_DIR}/examples/example1.cpp)
add_executable(example2 \${CMAKE_CURRENT_SOURCE_DIR}/examples/example2.cpp)
EOF
```

```sh
$ cat >> CMakeLists.txt <<EOF

target_link_libraries(example1 print)
target_link_libraries(example2 print)
EOF
```

Я сразу записала всё в CMakeLists.txt по этому в выводе союирается все сразу

Команда:

```sh
$ cmake -H. -B_build
```

Вывод:
```sh
CMake Deprecation Warning at CMakeLists.txt:1 (cmake_minimum_required):
  Compatibility with CMake < 3.5 will be removed from a future version of
  CMake.

  Update the VERSION argument <min> value or use a ...<max> suffix to tell
  CMake that the project does not need compatibility with older versions.


-- The C compiler identification is GNU 13.3.0
-- The CXX compiler identification is GNU 13.3.0
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Check for working C compiler: /usr/bin/cc - skipped
-- Detecting C compile features
-- Detecting C compile features - done
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Check for working CXX compiler: /usr/bin/c++ - skipped
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Configuring done (0.6s)
-- Generating done (0.0s)
-- Build files have been written to: /home/vboxuser/kasirskaaalina09-gif/workspace/projects/lab03/_build
```

Команда:

```sh
$ cmake --build _build
```

Вывод:

```sh
[ 16%] Building CXX object CMakeFiles/print.dir/sources/print.cpp.o
[ 33%] Linking CXX static library libprint.a
[ 33%] Built target print
[ 50%] Building CXX object CMakeFiles/example1.dir/examples/example1.cpp.o
[ 66%] Linking CXX executable example1
[ 66%] Built target example1
[ 83%] Building CXX object CMakeFiles/example2.dir/examples/example2.cpp.o
[100%] Linking CXX executable example2
[100%] Built target example2
```

Команда:

```sh
$ cmake --build _build --target print
```

Вывод:

```sh
[100%] Built target print
```

Команда:

```sh
cmake --build _build --target example1
```

Вывод:

```sh
[ 50%] Built target print
[100%] Built target example1
```

Команда:

```sh
cmake --build _build --target example2
```

Вывод:

```sh
[ 50%] Built target print
[100%] Built target example2
```

Команда:

```sh
$ ls -la _build/libprint.a
```

Вывод:

```sh
-rw-rw-r-- 1 vboxuser vboxuser 2246 May  9 12:13 _build/libprint.a
```

Команда:

```sh
$ _build/example1 && echo
```

Вывод:

```sh
hello
```

```sh
$ _build/example2
```

Команда:

```sh
$ cat log.txt && echo
```

Вывод:

```sh
hello
```

```sh
rm -rf log.txt
```

Команда:

```sh
git clone https://github.com/tp-labs/lab03 tmp
```

Вывод:

```sh
Cloning into 'tmp'...
remote: Enumerating objects: 91, done.
remote: Counting objects: 100% (30/30), done.
remote: Compressing objects: 100% (9/9), done.
remote: Total 91 (delta 23), reused 21 (delta 21), pack-reused 61 (from 1)
Receiving objects: 100% (91/91), 1.02 MiB | 3.21 MiB/s, done.
Resolving deltas: 100% (41/41), done.
```

```sh
$ mv -f tmp/CMakeLists.txt .
$ rm -rf tmp
```

```sh
$ cat CMakeLists.txt
```

Вывод:

```sh
cmake_minimum_required(VERSION 3.4)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

option(BUILD_EXAMPLES "Build examples" OFF)

project(print)

add_library(print STATIC ${CMAKE_CURRENT_SOURCE_DIR}/sources/print.cpp)

target_include_directories(print PUBLIC
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
  $<INSTALL_INTERFACE:include>
)

if(BUILD_EXAMPLES)
  file(GLOB EXAMPLE_SOURCES "${CMAKE_CURRENT_SOURCE_DIR}/examples/*.cpp")
  foreach(EXAMPLE_SOURCE ${EXAMPLE_SOURCES})
    get_filename_component(EXAMPLE_NAME ${EXAMPLE_SOURCE} NAME_WE)
    add_executable(${EXAMPLE_NAME} ${EXAMPLE_SOURCE})
    target_link_libraries(${EXAMPLE_NAME} print)
    install(TARGETS ${EXAMPLE_NAME}
      RUNTIME DESTINATION bin
    )
  endforeach(EXAMPLE_SOURCE ${EXAMPLE_SOURCES})
endif()

install(TARGETS print
    EXPORT print-config
    ARCHIVE DESTINATION lib
    LIBRARY DESTINATION lib
)

install(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/include/ DESTINATION include)
install(EXPORT print-config DESTINATION cmake)
```

Команда:

```sh
cmake -H. -B_build -DCMAKE_INSTALL_PREFIX=_install
```

Вывод:

```sh
CMake Deprecation Warning at CMakeLists.txt:1 (cmake_minimum_required):
  Compatibility with CMake < 3.5 will be removed from a future version of
  CMake.

  Update the VERSION argument <min> value or use a ...<max> suffix to tell
  CMake that the project does not need compatibility with older versions.


-- Configuring done (0.0s)
-- Generating done (0.0s)
-- Build files have been written to: /home/vboxuser/kasirskaaalina09-gif/workspace/projects/lab03/_build
```

Команда:

```sh
cmake --build _build --target install
```

Вывод:

```sh
[100%] Built target print
Install the project...
-- Install configuration: ""
-- Installing: /home/vboxuser/kasirskaaalina09-gif/workspace/projects/lab03/_install/lib/libprint.a
-- Installing: /home/vboxuser/kasirskaaalina09-gif/workspace/projects/lab03/_install/include
-- Installing: /home/vboxuser/kasirskaaalina09-gif/workspace/projects/lab03/_install/include/print.hpp
-- Installing: /home/vboxuser/kasirskaaalina09-gif/workspace/projects/lab03/_install/cmake/print-config.cmake
-- Installing: /home/vboxuser/kasirskaaalina09-gif/workspace/projects/lab03/_install/cmake/print-config-noconfig.cmake
```

Команда:

```sh
$ tree _install
```

Вывод:

```sh
Command 'tree' not found, but can be installed with:
sudo snap install tree  # version 2.1.3+pkg-5852, or
sudo apt  install tree  # version 2.1.1-2ubuntu3.24.04.2
See 'snap info tree' for additional versions.
```

Команда:

```sh
$ sudo apt update
```

Вывод:

```sh
[sudo] password for vboxuser: 
Get:1 http://security.ubuntu.com/ubuntu noble-security InRelease [126 kB]
Hit:2 http://ru.archive.ubuntu.com/ubuntu noble InRelease               
Get:3 http://ru.archive.ubuntu.com/ubuntu noble-updates InRelease [126 kB]
Hit:4 http://ru.archive.ubuntu.com/ubuntu noble-backports InRelease
Get:5 http://ru.archive.ubuntu.com/ubuntu noble-updates/main amd64 Packages [1,919 kB]
Get:6 http://security.ubuntu.com/ubuntu noble-security/main amd64 Packages [1,624 kB]
Get:7 http://ru.archive.ubuntu.com/ubuntu noble-updates/restricted amd64 Packages [3,056 kB]
Get:8 http://ru.archive.ubuntu.com/ubuntu noble-updates/restricted Translation-en [703 kB]  
Get:9 http://ru.archive.ubuntu.com/ubuntu noble-updates/universe amd64 Packages [1,685 kB]      
Get:10 http://security.ubuntu.com/ubuntu noble-security/universe amd64 Packages [1,182 kB]      
Fetched 10.4 MB in 2s (4,689 kB/s)                          
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
184 packages can be upgraded. Run 'apt list --upgradable' to see them.
```

Команда:

```sh
sudo apt install tree
```

Вывод:

```sh
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following NEW packages will be installed:
  tree
0 upgraded, 1 newly installed, 0 to remove and 184 not upgraded.
Need to get 47.4 kB of archives.
After this operation, 111 kB of additional disk space will be used.
Get:1 http://ru.archive.ubuntu.com/ubuntu noble-updates/universe amd64 tree amd64 2.1.1-2ubuntu3.24.04.2 [47.4 kB]
Fetched 47.4 kB in 0s (190 kB/s)
Selecting previously unselected package tree.
(Reading database ... 259999 files and directories currently installed.)
Preparing to unpack .../tree_2.1.1-2ubuntu3.24.04.2_amd64.deb ...
Unpacking tree (2.1.1-2ubuntu3.24.04.2) ...
Setting up tree (2.1.1-2ubuntu3.24.04.2) ...
Processing triggers for man-db (2.12.0-4build2) ...
```

```sh
$ git add CMakeLists.txt
$ git commit -m"added CMakeLists.txt"
```

```sh
$ git push origin master
```

## Report

```sh
$ popd
$ export LAB_NUMBER=03
$ git clone https://github.com/tp-labs/lab${LAB_NUMBER} tasks/lab${LAB_NUMBER}
$ mkdir reports/lab${LAB_NUMBER}
$ cp tasks/lab${LAB_NUMBER}/README.md reports/lab${LAB_NUMBER}/REPORT.md
$ cd reports/lab${LAB_NUMBER}
$ edit REPORT.md
$ gist REPORT.md
```

## Homework

Представьте, что вы стажер в компании "Formatter Inc.".
### Задание 1
Вам поручили перейти на систему автоматизированной сборки **CMake**.
Исходные файлы находятся в директории [formatter_lib](formatter_lib).
В этой директории находятся файлы для статической библиотеки *formatter*.
Создайте `CMakeList.txt` в директории [formatter_lib](formatter_lib),
с помощью которого можно будет собирать статическую библиотеку *formatter*.

Создаем папку formatter_lib и перемещаемся в эту папку 
Далее в этой папке создаем файл CMakeLists.txt, туда помещаем следующий текст:

```sh
```cmake
cmake_minimum_required(VERSION 3.4)

project(formatter)

add_library(formatter STATIC formatter.cpp)

target_include_directories(formatter PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})
```

Создаем formatter.cpp со следующим кодом:

```sh
#include "formatter.h"

std::string formatter(const std::string& message)
{
    std::string res;
    res += "-------------------------\n";
    res += message + "\n";
    res += "-------------------------\n";
    return res;
}
```

Также создаем и записываем код в formatter.h:

```sh
#pragma once

#include <string>

std::string formatter(const std::string& message);
```

### Задание 2
У компании "Formatter Inc." есть перспективная библиотека,
которая является расширением предыдущей библиотеки. Т.к. вы уже овладели
навыком созданием `CMakeList.txt` для статической библиотеки *formatter*, ваш 
руководитель поручает заняться созданием `CMakeList.txt` для библиотеки 
*formatter_ex*, которая в свою очередь использует библиотеку *formatter*.

Теперь уже для formatter_ex_lib создаем файл CMakeLists.txt

Содержимое файла:

```sh
cmake_minimum_required(VERSION 3.4)

project(formatter_ex)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_library(formatter_ex STATIC formatter_ex.cpp)

target_include_directories(formatter_ex PUBLIC
    ${CMAKE_CURRENT_SOURCE_DIR}
    ../formatter_lib
)

target_link_libraries(formatter_ex formatter)
```

Также создаем файлы formatter_ex.cpp

Cодержимое файла:

```sh
include "formatter_ex.h"

#include "formatter.h"

std::ostream& formatter(std::ostream& out, const std::string& message)
{
    return out << formatter(message);
}
```

И файл formatter_ex.h

Содержимое файла:

```sh
pragma once

#include <string>
#include <iostream>

std::ostream& formatter(std::ostream& out, const std::string& message);
```

### Задание 3
Конечно же ваша компания предоставляет примеры использования своих библиотек.
Чтобы продемонстрировать как работать с библиотекой *formatter_ex*,
вам необходимо создать два `CMakeList.txt` для двух простых приложений:
* *hello_world*, которое использует библиотеку *formatter_ex*;
* *solver*, приложение которое испольует статические библиотеки *formatter_ex* и *solver_lib*.

**Удачной стажировки!**

Создаем папку hello_world_application, в этой же папке создаем CMakeLists.txt и помещаем туда следующий текст:

```sh
cmake_minimum_required(VERSION 3.4)

project(hello_world)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_executable(hello_world hello_world.cpp)

target_link_libraries(hello_world formatter_ex)
```

Далее создаем файл hello_world.cpp со следующим содержимым:

```sh
#include <iostream>

#include "formatter_ex.h"

int main()
{
    formatter(std::cout, "hello, world!");
}
```

И аналагично для solver_application:

Cодержимле CMakeLists.txt:

```sh
cmake_minimum_required(VERSION 3.4)

project(solver_application)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_executable(solver_application equation.cpp)

target_link_libraries(solver_application formatter_ex solver)
```

Содержимое equation.cpp

```sh
#include <iostream>

#include "formatter_ex.h"
#include "solver.h"

int main()
{
    float a = 0;
    float b = 0;
    float c = 0;

    std::cin >> a >> b >> c;

    float x1 = 0;
    float x2 = 0;

    try
    {
        solve(a, b, c, x1, x2);

        formatter(std::cout, "x1 = " + std::to_string(x1));
        formatter(std::cout, "x2 = " + std::to_string(x2));
    }
    catch (const std::logic_error& ex)
    {
        formatter(std::cout, ex.what());
    }

    return 0;
}
```

Теперь также и для папки solver_lib:

Содержимое CMakeLists.txt:

```sh
cmake_minimum_required(VERSION 3.4)

project(solver)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_library(solver STATIC solver.cpp)

target_include_directories(solver PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})
```

Содержимое solver.cpp:

```sh
#include "solver.h"
#include <cmath>
#include <stdexcept>

void solve(float a, float b, float c, float& x1, float& x2)
{
    float d = (b * b) - (4 * a * c);

    if (d < 0)
    {
        throw std::logic_error{"error: discriminant < 0"};
    }

    x1 = (-b - std::sqrt(d)) / (2 * a);
    x2 = (-b + std::sqrt(d)) / (2 * a);
}
```

Содержимое solver.h:

```sh
#pragma once

void solve(float a, float b, float c, float& x1, float& x2);
```

Далее в CMakeLists.txt в основной папке lab03 вставляем следующее:

```sh
cmake_minimum_required(VERSION 3.4)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

option(BUILD_EXAMPLES "Build examples" OFF)

project(print)

add_library(print STATIC ${CMAKE_CURRENT_SOURCE_DIR}/sources/print.cpp)

target_include_directories(print PUBLIC
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
  $<INSTALL_INTERFACE:include>
)

if(BUILD_EXAMPLES)
  file(GLOB EXAMPLE_SOURCES "${CMAKE_CURRENT_SOURCE_DIR}/examples/*.cpp")
  foreach(EXAMPLE_SOURCE ${EXAMPLE_SOURCES})
    get_filename_component(EXAMPLE_NAME ${EXAMPLE_SOURCE} NAME_WE)
    add_executable(${EXAMPLE_NAME} ${EXAMPLE_SOURCE})
    target_link_libraries(${EXAMPLE_NAME} print)
    install(TARGETS ${EXAMPLE_NAME}
      RUNTIME DESTINATION bin
    )
  endforeach(EXAMPLE_SOURCE ${EXAMPLE_SOURCES})
endif()

install(TARGETS print
    EXPORT print-config
    ARCHIVE DESTINATION lib
    LIBRARY DESTINATION lib
)

install(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/include/ DESTINATION include)
install(EXPORT print-config DESTINATION cmake)

add_subdirectory(formatter_lib)
add_subdirectory(formatter_ex_lib)
add_subdirectory(solver_lib)
add_subdirectory(hello_world_application)
add_subdirectory(solver_application)
```

Теперь мы можем переместиться в корень папки lab03 и собрать проект:

Команда:

```sh
$ cmake -H. -B_build
```

Вывод:

```sh
CMake Deprecation Warning at CMakeLists.txt:1 (cmake_minimum_required):
  Compatibility with CMake < 3.5 will be removed from a future version of
  CMake.

  Update the VERSION argument <min> value or use a ...<max> suffix to tell
  CMake that the project does not need compatibility with older versions.


-- The C compiler identification is GNU 13.3.0
-- The CXX compiler identification is GNU 13.3.0
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Check for working C compiler: /usr/bin/cc - skipped
-- Detecting C compile features
-- Detecting C compile features - done
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Check for working CXX compiler: /usr/bin/c++ - skipped
-- Detecting CXX compile features
-- Detecting CXX compile features - done
CMake Deprecation Warning at formatter_lib/CMakeLists.txt:1 (cmake_minimum_required):
  Compatibility with CMake < 3.5 will be removed from a future version of
  CMake.

  Update the VERSION argument <min> value or use a ...<max> suffix to tell
  CMake that the project does not need compatibility with older versions.


CMake Deprecation Warning at formatter_ex_lib/CMakeLists.txt:1 (cmake_minimum_required):
  Compatibility with CMake < 3.5 will be removed from a future version of
  CMake.

  Update the VERSION argument <min> value or use a ...<max> suffix to tell
  CMake that the project does not need compatibility with older versions.


CMake Deprecation Warning at solver_lib/CMakeLists.txt:1 (cmake_minimum_required):
  Compatibility with CMake < 3.5 will be removed from a future version of
  CMake.
 Update the VERSION argument <min> value or use a ...<max> suffix to tell
  CMake that the project does not need compatibility with older versions.


CMake Deprecation Warning at hello_world_application/CMakeLists.txt:1 (cmake_minimum_required):
  Compatibility with CMake < 3.5 will be removed from a future version of
  CMake.

  Update the VERSION argument <min> value or use a ...<max> suffix to tell
  CMake that the project does not need compatibility with older versions.


CMake Deprecation Warning at solver_application/CMakeLists.txt:1 (cmake_minimum_required):
  Compatibility with CMake < 3.5 will be removed from a future version of
  CMake.

  Update the VERSION argument <min> value or use a ...<max> suffix to tell
  CMake that the project does not need compatibility with older versions.


-- Configuring done (0.5s)
-- Generating done (0.0s)
-- Build files have been written to: /home/vboxuser/kasirskaaalina09-gif/workspace/projects/lab03/_build
```

Команда:

```sh
$ cmake --build _build
```

Вывод:

```sh
[  8%] Building CXX object CMakeFiles/print.dir/sources/print.cpp.o
[ 16%] Linking CXX static library libprint.a
[ 16%] Built target print
[ 25%] Building CXX object formatter_lib/CMakeFiles/formatter.dir/formatter.cpp.o
[ 33%] Linking CXX static library libformatter.a
[ 33%] Built target formatter
[ 41%] Building CXX object formatter_ex_lib/CMakeFiles/formatter_ex.dir/formatter_ex.cpp.o
[ 50%] Linking CXX static library libformatter_ex.a
[ 50%] Built target formatter_ex
[ 58%] Building CXX object solver_lib/CMakeFiles/solver.dir/solver.cpp.o
[ 66%] Linking CXX static library libsolver.a
[ 66%] Built target solver
[ 75%] Building CXX object hello_world_application/CMakeFiles/hello_world.dir/hello_world.cpp.o
[ 83%] Linking CXX executable hello_world
[ 83%] Built target hello_world
[ 91%] Building CXX object solver_application/CMakeFiles/solver_application.dir/equation.cpp.o
[100%] Linking CXX executable solver_application
[100%] Built target solver_application
```

Теперь мы можем запустить проекты:

Команда:

```sh
./_build/hello_world_application/hello_world
```

Вывод:

```sh
-------------------------
hello, world!
-------------------------
```

Команда:

```sh
./_build/solver_application/solver_application
```

Вывод:

```sh
1 6 -7
-------------------------
x1 = -7.000000
-------------------------
-------------------------
x2 = 1.000000
-------------------------
```

## Links
- [Основы сборки проектов на С/C++ при помощи CMake](https://eax.me/cmake/)
- [CMake Tutorial](http://neerc.ifmo.ru/wiki/index.php?title=CMake_Tutorial)
- [C++ Tutorial - make & CMake](https://www.bogotobogo.com/cplusplus/make.php)
- [Autotools](http://www.gnu.org/software/automake/manual/html_node/Autotools-Introduction.html)
- [CMake](https://cgold.readthedocs.io/en/latest/index.html)

```
Copyright (c) 2015-2021 The ISC Authors
```
