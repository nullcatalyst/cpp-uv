cmake_minimum_required(VERSION 3.10)

project(uv)

add_library(uv "")

target_include_directories(uv
PUBLIC
    include
PRIVATE
    src
)

target_sources(uv
PRIVATE
    # include/uv.h
    # include/tree.h
    # include/uv-errno.h
    # include/uv-threadpool.h
    # include/uv-version.h
    src/fs-poll.c
    # src/heap-inl.h
    src/inet.c
    # src/queue.h
    src/threadpool.c
    src/uv-data-getter-setters.c
    src/uv-common.c
    # src/uv-common.h
    src/version.c
)

if(WIN32)
    target_sources(uv
    PRIVATE
        # include/uv-win.h
        src/win/async.c
        # src/win/atomicops-inl.h
        src/win/core.c
        src/win/detect-wakeup.c
        src/win/dl.c
        src/win/error.c
        src/win/fs.c
        src/win/fs-event.c
        src/win/getaddrinfo.c
        src/win/getnameinfo.c
        src/win/handle.c
        # src/win/handle-inl.h
        # src/win/internal.h
        src/win/loop-watcher.c
        src/win/pipe.c
        src/win/thread.c
        src/win/poll.c
        src/win/process.c
        src/win/process-stdio.c
        src/win/req.c
        # src/win/req-inl.h
        src/win/signal.c
        src/win/snprintf.c
        src/win/stream.c
        # src/win/stream-inl.h
        src/win/tcp.c
        src/win/tty.c
        src/win/timer.c
        src/win/udp.c
        src/win/util.c
        src/win/winapi.c
        # src/win/winapi.h
        src/win/winsock.c
        src/win/winsock.h
    )

    target_compile_definitions(uv
    PRIVATE
        _WIN32_WINNT=0x0600
        _GNU_SOURCE
    )

    target_link_libraries(uv
    PRIVATE
        advapi32
        iphlpapi
        psapi
        shell32
        user32
        userenv
        ws2_32
    )
else(WIN32)
    target_sources(uv
    PRIVATE
        # include/uv-unix.h
        # include/uv-linux.h
        # include/uv-sunos.h
        # include/uv-darwin.h
        # include/uv-bsd.h
        # include/uv-aix.h
        src/unix/async.c
        # src/unix/atomic-ops.h
        src/unix/core.c
        src/unix/dl.c
        src/unix/fs.c
        src/unix/getaddrinfo.c
        src/unix/getnameinfo.c
        # src/unix/internal.h
        src/unix/loop.c
        src/unix/loop-watcher.c
        src/unix/pipe.c
        src/unix/poll.c
        src/unix/process.c
        src/unix/signal.c
        # src/unix/spinlock.h
        src/unix/stream.c
        src/unix/tcp.c
        src/unix/thread.c
        src/unix/timer.c
        src/unix/tty.c
        src/unix/udp.c
    )

    target_link_libraries(uv
    PUBLIC
        m
    )

    # -pthread

    # if(shared library)
    # -fPIC
    # -DBUILDING_UV_SHARED=1
    # endif()

    if(${CMAKE_SYSTEM_NAME} STREQUAL "Linux")
        target_sources(uv
        PRIVATE
            src/unix/proctitle.c

            src/unix/linux-core.c
            src/unix/linux-inotify.c
            src/unix/linux-syscalls.c
            # src/unix/linux-syscalls.h
            src/unix/procfs-exepath.c
            src/unix/sysinfo-loadavg.c
            src/unix/sysinfo-memory.c
        )

        target_compile_definitions(uv
        PRIVATE
            _GNU_SOURCE
            _POSIX_C_SOURCE=200112
        )
    elseif(${CMAKE_SYSTEM_NAME} STREQUAL "Darwin")
        target_sources(uv
        PRIVATE
            src/unix/proctitle.c

            src/unix/darwin.c
            src/unix/fsevents.c
            src/unix/darwin-proctitle.c

            src/unix/bsd-ifaddrs.c
            src/unix/kqueue.c
        )

        target_compile_definitions(uv
        PRIVATE
            _DARWIN_USE_64_BIT_INODE=1
            _DARWIN_UNLIMITED_SELECT=1
        )
    endif()
endif(WIN32)
