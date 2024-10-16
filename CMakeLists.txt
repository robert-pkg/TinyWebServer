cmake_minimum_required(VERSION 3.9 FATAL_ERROR)
project(TinyWebServer VERSION 0.1.0 LANGUAGES C CXX)

# SET 用来显式的定义变量
SET(CMAKE_CXX_STANDARD 17)

add_executable(TinyWebServer.exe main.cpp  ./timer/lst_timer.cpp ./http/http_conn.cpp ./log/log.cpp ./CGImysql/sql_connection_pool.cpp webserver.cpp config.cpp)

target_include_directories(TinyWebServer.exe PRIVATE /data/mysql/include)
target_link_directories(TinyWebServer.exe PRIVATE /data/mysql/lib)
target_link_libraries(TinyWebServer.exe PRIVATE pthread mysqlclient)

