# 前言

命令使用`open`、`read`、`write`、`lseek`。


# 作业
## 创建、写入、读取文件
``` c
#include <stdio.h>
#include <string.h>

#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>
#include <unistd.h>

int main() {
    int fd;
    int ret;
    char buffer[50];

    fd = open("test.txt", O_CREAT | O_WRONLY, S_IROTH | S_IRGRP | S_IRUSR | S_IWUSR);

    if (fd == -1) {
        printf("Error opening or create file");
        return 1;
    }

    printf("Create or Open File Success\n");

    ret = write(fd, "Hello, World!\n", strlen("Hello, World!\n"));

    if (ret == -1) {
        printf("Error writing to file");
        close(fd);
        return 1;
    }

    printf("Wrote file successfully\n");

    close(fd);

    fd = open("test.txt", O_RDONLY);

    if( fd == -1) {
        printf("Error opening file for reading");
        return 1;
    }

    printf("Open file for reading success\n");

    ret = read(fd, buffer, sizeof(buffer) - 1);
    
    if (ret == -1) {
        printf("Error reading from file");
        close(fd);
        return 1;
    }

    printf("Read %d bytes: %s", ret, buffer);

    ret = lseek(fd, 0, SEEK_END);

    if( ret == -1) {
        printf("Error seeking to end of file\n");
        close(fd);
        return 1;
    }

    printf("File Size: %d bytes\n", ret);

    close(fd);

    return 0;
}
```
## 将1进行备份成1_back

``` c
#include <stdio.h>

#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>

 #include <unistd.h>

// 获取文件大小
// 使用1.c生成的1可执行程序进行赋值到1_back

char buffer[1024<<5];

int main() {
    int fd;
    int fd1;
    size_t Fsize;
    int ret;

    fd = open("1", O_RDONLY);

    if( fd == -1) {
        printf("Error opening file for reading\n");
        return 1;
    }

    printf("Open file for reading success\n");

    Fsize = lseek(fd, 0, SEEK_END);

    if( Fsize == -1) {
        printf("Error seeking to end of file\n");
        close(fd);
        return 1;
    }

    printf("File size is %ld bytes\n", Fsize);

    if( Fsize > sizeof(buffer)) {
        printf("File size is larger than buffer size\n");
        close(fd);
        return 1;
    }

    ret = lseek(fd, 0, SEEK_SET);

    if( ret == -1) {
        printf("Error seeking to beginning of file\n");
        close(fd);
        return 1;
    }

    ret = read(fd, buffer, Fsize);

    if( ret != Fsize) {
        printf("Error reading file\n");
        close(fd);
        return 1;
    }

    printf("Read file success\n");

    close(fd);

    fd1 = open("1_back", O_CREAT | O_WRONLY | O_TRUNC, 0700);

    if( fd1 == -1) {
        printf("Error opening or creating backup file\n");
        close(fd);
        return 1;
    }

    printf("Open or create backup file success\n");

    ret = write(fd1, buffer, Fsize);

    if( ret != Fsize) {
        printf("Error writing to backup file\n");
        close(fd1);
        return 1;
    }

    printf("Write to backup file success\n");

    close(fd1);

    return 0;
}
```

## 通过命令行参数进行备份文件

``` c
#include <stdio.h>

#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>

 #include <unistd.h>

// 获取文件大小
// 使用命令行传参方式生成的某个文件的备份文件

char buffer[1024<<5];

int main(int argc, char *argv[]) {
    int fd;
    int fd1;
    size_t Fsize;
    int ret;

    printf("argc=%d\n", argc);

    for(int i = 0; i < argc; i++) {
        printf("argv[%d]=%s\n", i, argv[i]);
    }

    for(int i = 1; i < argc; i++) {
        fd = open(argv[i], O_RDONLY);

        if( fd == -1) {
            printf("Error opening file for reading\n");
            return 1;
        }

        printf("Open file for reading success\n");

        Fsize = lseek(fd, 0, SEEK_END);

        if( Fsize == -1) {
            printf("Error seeking to end of file\n");
            close(fd);
            return 1;
        }

        printf("File size is %ld bytes\n", Fsize);

        if( Fsize > sizeof(buffer)) {
            printf("File size is larger than buffer size\n");
            close(fd);
            return 1;
        }

        ret = lseek(fd, 0, SEEK_SET);

        if( ret == -1) {
            printf("Error seeking to beginning of file\n");
            close(fd);
            return 1;
        }

        ret = read(fd, buffer, Fsize);

        if( ret != Fsize) {
            printf("Error reading file\n");
            close(fd);
            return 1;
        }

        printf("Read file success\n");

        close(fd);

        char backup_filename[100];
        snprintf(backup_filename, sizeof(backup_filename), "%s_back", argv[i]);

        fd1 = open(backup_filename, O_CREAT | O_WRONLY | O_TRUNC, 0700);

        if( fd1 == -1) {
            printf("Error opening or creating backup file\n");
            close(fd);
            return 1;
        }

        printf("Open or create backup file success\n");

        ret = write(fd1, buffer, Fsize);

        if( ret != Fsize) {
            printf("Error writing to backup file\n");
            close(fd1);
            return 1;
        }

        printf("Write to backup file success\n");

        close(fd1);   
    }

    return 0;
}
```