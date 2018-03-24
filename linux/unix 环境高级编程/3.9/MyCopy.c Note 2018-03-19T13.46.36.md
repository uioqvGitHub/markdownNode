MyCopy.c Note 2018-03-19T13.46.36
========================

### src/MyCopy.c

    #include "apue.h"
    
    #define BUFFSIZE 4096
    
    int main(void) {
        int n;
        char buf[BUFFSIZE];
    
    
        while((n = read(STDIN_FILENO, buf, BUFFSIZE)) > 0)
            if(write(STDOUT_FILENO, buf, n) != n)  
                err_sys("write error");
        if(n < 0)  
            err_sys("read error");
    
        exit(0);
        
    }

### Makefile

	DIR_SRC = ./src
	DIR_INC = ./include
	DIR_OBJ = ./obj
	DIR_BIN = ./bin
	
	SRC = $(wildcard ./src/*.c)
	OBJ = $(patsubst %.c,${DIR_OBJ}/%.o, $(notdir ${SRC}))
	
	TARGET = mycopy
	
	BIN_TARGET = ${DIR_BIN}/${TARGET}
	
	CC = gcc 
	CFLAGS = -g -Wall -I${DIR_INC}
	
	${BIN_TARGET}:${OBJ}
	    $(CC) $(CFLAGS) $(OBJ) -o $@
	
	${DIR_OBJ}/%.o:${DIR_SRC}/%.c
	    $(CC) $(CFLAGS) -c $< -o $@
	
	.PHONY:clean
	clean:
	    -rm ${DIR_OBJ}/*.o ${BIN_TARGET}

