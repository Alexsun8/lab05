## Laboratory work V

Данная лабораторная работа посвещена изучению фреймворков для тестирования на примере **GTest**

```ShellSession
$ open https://github.com/google/googletest
```

## Tasks

- [x] 1. Создать публичный репозиторий с названием **lab05** на сервисе **GitHub**
- [x] 2. Выполнить инструкцию учебного материала
- [x] 3. Ознакомиться со ссылками учебного материала
- [x] 4. Составить отчет и отправить ссылку личным сообщением в **Slack**

## Tutorial

```ShellSession
$ export GITHUB_USERNAME=<имя_пользователя> //присваиваем переменной значеие
$ alias gsed=sed # for *-nix system  //при написании gsed запускать команду sed
```

```ShellSession
$ cd ${GITHUB_USERNAME}/workspace //переходим в директорию 
$ pushd . //сохраняем директорию 
$ source scripts/activate //запускаем файл
```

```ShellSession
$ git clone https://github.com/${GITHUB_USERNAME}/lab04 projects/lab05  //клонируем репозиторий
$ cd projects/lab05 //перерзодим в директорию
$ git remote remove origin //удаляем ссылку на старый репозиторий с ветки origin
$ git remote add origin https://github.com/${GITHUB_USERNAME}/lab05 //заново определяем origin
```

```ShellSession
$ mkdir third-party //создаем директорию
$ git submodule add https://github.com/google/googletest third-party/gtest //клонирование репозитория в подкаталог
$ cd third-party/gtest && git checkout release-1.8.1 && cd ../.. //переход ы папку, пеерзод на ведку, выход в изначальную директорию
$ git add third-party/gtest //добавление файлов
$ git commit -m"added gtest framework" //коммит
```

```ShellSession
$ gsed -i '/option(BUILD_EXAMPLES "Build examples" OFF)/a\ //добавляет файл в место
option(BUILD_TESTS "Build tests" OFF)
' CMakeLists.txt
$ cat >> CMakeLists.txt <<EOF \\запись в файл

if(BUILD_TESTS)
  enable_testing()
  add_subdirectory(third-party/gtest)
  file(GLOB \${PROJECT_NAME}_TEST_SOURCES tests/*.cpp)
  add_executable(check \${\${PROJECT_NAME}_TEST_SOURCES})
  target_link_libraries(check \${PROJECT_NAME} gtest_main)
  add_test(NAME check COMMAND check)
endif()
EOF
```

```ShellSession
$ mkdir tests //создание директории
$ cat > tests/test1.cpp <<EOF //запись в файл
#include <print.hpp>

#include <gtest/gtest.h>

TEST(Print, InFileStream)
{
  std::string filepath = "file.txt";
  std::string text = "hello";
  std::ofstream out{filepath};

  print(text, out);
  out.close();

  std::string result;
  std::ifstream in{filepath};
  in >> result;

  EXPECT_EQ(result, text);
}
EOF
```

```ShellSession
$ cmake -H. -B_build -DBUILD_TESTS=ON //сборка с вкл тестами
$ cmake --build _build //сборка и вывод результатов борки
$ cmake --build _build --target test //сборка и вывод работы тестов
```

```ShellSession
$ _build/check //проверка
$ cmake --build _build --target test -- ARGS=--verbose //тесты
```

```ShellSession
$ gsed -i 's/lab04/lab05/g' README.md //запись
$ gsed -i 's/\(DCMAKE_INSTALL_PREFIX=_install\)/\1 -DBUILD_TESTS=ON/' .travis.yml //вкл тесты
$ gsed -i '/cmake --build _build --target install/a\
- cmake --build _build --target test -- ARGS=--verbose
' .travis.yml
```

```ShellSession
$ travis lint //предупреждения
```

```ShellSession
$ git add .travis.yml //добавление всего и отправление на удаленный репозиторий 
$ git add tests
$ git add -p
$ git commit -m"added tests"
$ git push origin master
```

```ShellSession
$ travis login --auto  //авторизация в travis
$ travis enable //вкл проект
```

```ShellSession
$ mkdir artifacts //создать директорию
$ sleep 20s && gnome-screenshot --file artifacts/screenshot.png //заснуть на 20с, сделать скриншот и сохранить его заданным файлом в заданной директории
# for macOS: $ screencapture -T 20 artifacts/screenshot.png 
# open https://github.com/${GITHUB_USERNAME}/lab05 //открыть гитхаб
```

## Report

```ShellSession
$ popd
$ export LAB_NUMBER=05
$ git clone https://github.com/tp-labs/lab${LAB_NUMBER} tasks/lab${LAB_NUMBER}
$ mkdir reports/lab${LAB_NUMBER}
$ cp tasks/lab${LAB_NUMBER}/README.md reports/lab${LAB_NUMBER}/REPORT.md
$ cd reports/lab${LAB_NUMBER}
$ edit REPORT.md
$ gistup -m "lab${LAB_NUMBER}"
```

## Homework

### Задание
1. Создайте `CMakeList.txt` для библиотеки *banking*.
2. Создайте модульные тесты на классы `Transaction` и `Account`.
    * Используйте mock-объекты.
    * Покрытие кода должно составлять 100%.
3. Настройте сборочную процедуру на **TravisCI**.
4. Настройте [Coveralls.io](https://coveralls.io/).

## Links

- [C++ CI: Travis, CMake, GTest, Coveralls & Appveyor](http://david-grs.github.io/cpp-clang-travis-cmake-gtest-coveralls-appveyor/)
- [Boost.Tests](http://www.boost.org/doc/libs/1_63_0/libs/test/doc/html/)
- [Catch](https://github.com/catchorg/Catch2)

```
Copyright (c) 2015-2019 The ISC Authors
```
