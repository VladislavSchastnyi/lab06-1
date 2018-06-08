## Laboratory work VI

Данная лабораторная работа посвещена изучению фреймворков для тестирования на примере **Catch**

```ShellSession
$ open https://github.com/philsquared/Catch
```

## Tasks

- [ ] 1. Создать публичный репозиторий с названием **lab06** на сервисе **GitHub**
- [ ] 2. Выполнить инструкцию учебного материала
- [ ] 3. Ознакомиться со ссылками учебного материала
- [ ] 4. Составить отчет и отправить ссылку личным сообщением в **Slack**

## Tutorial

```ShellSession
$ export GITHUB_USERNAME=VladislavSchastnyi
$ alias gsed=sed # for *-nix system
```
Создание директории лабораторной на основе предыдущей

```ShellSession
$ git clone https://github.com/${GITHUB_USERNAME}/Lab05 lab06
Cloning into 'lab06'...
remote: Counting objects: 29, done.
remote: Compressing objects: 100% (18/18), done.
remote: Total 29 (delta 5), reused 29 (delta 5), pack-reused 0
Unpacking objects: 100% (29/29), done.
$ cd lab06
$ git remote remove origin
$ git remote add origin https://github.com/${GITHUB_USERNAME}/lab06
```
Скачивание библиотеки Catch и создание main с включенной билиотекой

```ShellSession
$ mkdir tests
$ wget https://github.com/philsquared/Catch/releases/download/v1.9.3/catch.hpp -O tests/catch.hpp
--2018-06-08 04:30:46--  https://github.com/philsquared/Catch/releases/download/v1.9.3/catch.hpp
Resolving github.com (github.com)... 192.30.253.113, 192.30.253.112
Connecting to github.com (github.com)|192.30.253.113|:443... connected.
HTTP request sent, awaiting response... 301 Moved Permanently
Location: https://github.com/catchorg/Catch2/releases/download/v1.9.3/catch.hpp [following]
--2018-06-08 04:30:46--  https://github.com/catchorg/Catch2/releases/download/v1.9.3/catch.hpp
Reusing existing connection to github.com:443.
HTTP request sent, awaiting response... 302 Found
Location: https://github-production-release-asset-2e65be.s3.amazonaws.com/1062572/61afa568-29c4-11e7-9755-a1211db07475?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAIWNJYAX4CSVEH53A%2F20180608%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20180608T043046Z&X-Amz-Expires=300&X-Amz-Signature=ce05ae08918d188f1944959b8d0ddd74f4e8b1dc20cb3ebd6ed4a29fbfe0c5de&X-Amz-SignedHeaders=host&actor_id=0&response-content-disposition=attachment%3B%20filename%3Dcatch.hpp&response-content-type=application%2Foctet-stream [following]
--2018-06-08 04:30:46--  https://github-production-release-asset-2e65be.s3.amazonaws.com/1062572/61afa568-29c4-11e7-9755-a1211db07475?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAIWNJYAX4CSVEH53A%2F20180608%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20180608T043046Z&X-Amz-Expires=300&X-Amz-Signature=ce05ae08918d188f1944959b8d0ddd74f4e8b1dc20cb3ebd6ed4a29fbfe0c5de&X-Amz-SignedHeaders=host&actor_id=0&response-content-disposition=attachment%3B%20filename%3Dcatch.hpp&response-content-type=application%2Foctet-stream
Resolving github-production-release-asset-2e65be.s3.amazonaws.com (github-production-release-asset-2e65be.s3.amazonaws.com)... 52.216.162.75
Connecting to github-production-release-asset-2e65be.s3.amazonaws.com (github-production-release-asset-2e65be.s3.amazonaws.com)|52.216.162.75|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 417675 (408K) [application/octet-stream]
Saving to: ‘tests/catch.hpp’

100%[===========================================================================================================================================================>] 417,675      382KB/s   in 1.1s   

2018-06-08 04:30:48 (382 KB/s) - ‘tests/catch.hpp’ saved [417675/417675]

$ cat > tests/main.cpp <<EOF
#define CATCH_CONFIG_MAIN
#include "catch.hpp"
EOF
```

```ShellSession
$ gsed -i '/option(BUILD_EXAMPLES "Build examples" OFF)/a\ #добавление строки в потоковый редактор
option(BUILD_TESTS "Build tests" OFF)
' CMakeLists.txt
$ cat >> CMakeLists.txt <<EOF #запись в файл

if(BUILD_TESTS)
	enable_testing() #включение теста
	file(GLOB \${PROJECT_NAME}_TEST_SOURCES tests/*.cpp) # поиск файла по заданному шаблону
	add_executable(check \${\${PROJECT_NAME}_TEST_SOURCES}) 
	target_link_libraries(check \${PROJECT_NAME} \${DEPENDS_LIBRARIES}) 
	#параметры теста. -s = успешные выполнения теста. -r compact = формат вывода
	add_test(NAME check COMMAND check "-s" "-r" "compact" "--use-colour" "yes") 
endif() #конец условного оператора
EOF 
```
Создание теста

```ShellSession
$ cat >> tests/test1.cpp <<EOF #запись в файл
#include "catch.hpp"
#include <print.hpp>

TEST_CASE("output values should match input values", "[file]") {
  std::string text = "hello";
  std::ofstream out("file.txt");
  
  print(text, out);
  out.close();
  
  std::string result;
  std::ifstream in("file.txt");
  in >> result;
  
  REQUIRE(result == text);
}
EOF
```
Сборка проекта и запуск

```ShellSession
$ cmake -H. -B_build -DBUILD_TESTS=ON #подготовка к процессу сборки
-- The C compiler identification is Clang 3.6.0
-- The CXX compiler identification is GNU 4.8.4
-- Check for working C compiler: /usr/bin/clang
-- Check for working C compiler: /usr/bin/clang -- works
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Check for working CXX compiler: /usr/bin/c++
-- Check for working CXX compiler: /usr/bin/c++ -- works
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Configuring done
-- Generating done
-- Build files have been written to: /home/ubuntu/workspace/lab06/_build
$ cmake --build _build #начало сборки
Scanning dependencies of target print
[ 33%] Building CXX object CMakeFiles/print.dir/sources/print.cpp.o
Linking CXX static library libprint.a
[ 33%] Built target print
Scanning dependencies of target check
[ 66%] Building CXX object CMakeFiles/check.dir/tests/test1.cpp.o
[100%] Building CXX object CMakeFiles/check.dir/tests/main.cpp.o
Linking CXX executable check
[100%] Built target check
$ cmake --build _build --target test #сборка test
Running tests...
Test project /home/ubuntu/workspace/lab06/_build
    Start 1: check
1/1 Test #1: check ............................   Passed    0.00 sec

100% tests passed, 0 tests failed out of 1

Total Test time (real) =   0.01 sec

```

```ShellSession
$ _build/check -s -r compact #результаты теста
/home/ubuntu/workspace/lab06/tests/test1.cpp:15: passed: result == text for: "hello" == "hello"
Passed 1 test case with 1 assertion.
$ cmake --build _build --target test -- ARGS=--verbose #сборка test, выводящую на экран результаты тестов
Running tests...
UpdateCTestConfiguration  from :/home/ubuntu/workspace/lab06/_build/DartConfiguration.tcl
UpdateCTestConfiguration  from :/home/ubuntu/workspace/lab06/_build/DartConfiguration.tcl
Test project /home/ubuntu/workspace/lab06/_build
Constructing a list of tests
Done constructing a list of tests
Checking test dependency graph...
Checking test dependency graph end
test 1
    Start 1: check

1: Test command: /home/ubuntu/workspace/lab06/_build/check "-s" "-r" "compact" "--use-colour" "yes"
1: Test timeout computed to be: 9.99988e+06
1: /home/ubuntu/workspace/lab06/tests/test1.cpp:15: passed: result == text for: "hello" == "hello"
1: Passed 1 test case with 1 assertion.
1: 
1/1 Test #1: check ............................   Passed    0.01 sec

100% tests passed, 0 tests failed out of 1

Total Test time (real) =   0.01 sec
```
Изменение файлов

```ShellSession
$ gsed -i 's/lab05/lab06/g' README.md
$ gsed -i 's/\(DCMAKE_INSTALL_PREFIX=_install\)/\1 -DBUILD_TESTS=ON/' .travis.yml
$ gsed -i '/cmake --build _build --target install/a\
- cmake --build _build --target test -- ARGS=--verbose
' .travis.yml
```

```ShellSession
$ travis lint #предупреждения для .travis.yml
Warnings for .travis.yml:
[x] value for addons section is empty, dropping
[x] in addons section: unexpected key apt, dropping
```
Коммит

```ShellSession
$ git add .
$ git commit -m"added tests"
[master d9f09fd] added tests
 6 files changed, 11505 insertions(+), 2 deletions(-)
 create mode 100644 file.txt
 create mode 100644 tests/catch.hpp
 create mode 100644 tests/main.cpp
 create mode 100644 tests/test1.cpp
$ git push origin master
Username for 'https://github.com': VladislavSchastnyi
Password for 'https://VladislavSchastnyi@github.com': 
Counting objects: 38, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (30/30), done.
Writing objects: 100% (38/38), 81.16 KiB | 0 bytes/s, done.
Total 38 (delta 8), reused 0 (delta 0)
remote: Resolving deltas: 100% (8/8), done.
To https://github.com/VladislavSchastnyi/lab06
 * [new branch]      master -> master
```

```ShellSession
$ travis login --auto #вход в свой аккаунт Travis
We need your GitHub login to identify you.
This information will not be sent to Travis CI, only to api.github.com.
The password will not be displayed.

Try running with --github-token or --auto if you don't want to enter your password anyway.

Username: VladislavSchastnyi
Password for VladislavSchastnyi: ***********
Successfully logged in as VladislavSchastnyi!
$ travis enable
Detected repository as VladislavSchastnyi/lab06, is this correct? |yes| y
VladislavSchastnyi/lab06: enabled :)
```
Скриншот и сохранение его в отдельную папку

```ShellSession
$ mkdir artifacts
$ sleep 20s && gnome-screenshot --file artifacts/screenshot.png
# for macOS: $ screencapture -T 20 artifacts/screenshot.png
# open https://github.com/${GITHUB_USERNAME}/lab06
```

## Report

```ShellSession
$ popd
$ export LAB_NUMBER=06
$ git clone https://github.com/tp-labs/lab${LAB_NUMBER} tasks/lab${LAB_NUMBER}
$ mkdir reports/lab${LAB_NUMBER}
$ cp tasks/lab${LAB_NUMBER}/README.md reports/lab${LAB_NUMBER}/REPORT.md
$ cd reports/lab${LAB_NUMBER}
$ edit REPORT.md
$ gistup -m "lab${LAB_NUMBER}"
```

## Links

- [Boost.Tests](http://www.boost.org/doc/libs/1_63_0/libs/test/doc/html/)
- [Google Test](https://github.com/google/googletest)

```
Copyright (c) 2017 Братья Вершинины
```
