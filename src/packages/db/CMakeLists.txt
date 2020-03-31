if(${PACKAGE_DB})
    add_library(package_db STATIC
            "db.cc"
            "db.h")

    target_compile_definitions(package_db PUBLIC "DEFAULT_DB=${PACKAGE_DB_DEFAULT_DB}")

    if(NOT ${PACKAGE_DB_MYSQL} EQUAL "")
        message(STATUS "PACKAGE_DB USE_MYSQL: ${PACKAGE_DB_MYSQL}")
        target_compile_options(package_db PUBLIC "${MYSQL_CXXFLAGS}")
        target_compile_definitions(package_db PUBLIC "USE_MYSQL=${PACKAGE_DB_MYSQL}")

        if(STATIC)
          set(MYSQLCLIENT_STATIC_LINKING TRUE)
        endif()

        find_package(MySQL REQUIRED)
        target_include_directories(package_db SYSTEM PUBLIC ${MYSQL_INCLUDE_DIR})

        target_link_libraries(package_db PRIVATE ${MYSQL_LINK_FLAGS})
        target_link_libraries(package_db PRIVATE ${MYSQL_LIB})
    endif()

    if(NOT ${PACKAGE_DB_SQLITE} EQUAL "")
      find_package(SQLite3 REQUIRED)
      message(STATUS "PACKAGE_DB USE_SQLITE3: ${PACKAGE_DB_SQLITE}, Found version ${SQLite3_VERSION}")

      target_compile_definitions(package_db PUBLIC "USE_SQLITE3=${PACKAGE_DB_SQLITE}")
      target_include_directories(package_db SYSTEM PUBLIC ${SQLite3_INCLUDE_DIRS})
      target_link_libraries(package_db PRIVATE ${SQLite3_LIBRARIES})
    endif()

    if (${PACKAGE_DB_DEFAULT_DB} EQUAL "" OR NOT (
            ${PACKAGE_DB_DEFAULT_DB} EQUAL ${PACKAGE_DB_MYSQL} OR
            ${PACKAGE_DB_DEFAULT_DB} EQUAL ${PACKAGE_DB_MYSQL} OR
            ${PACKAGE_DB_DEFAULT_DB} EQUAL ${PACKAGE_DB_MYSQL}
        ))
      message(FATAL "PACKAGE_DB_DEFAULT_DB is not valid!")
    endif()
endif()
