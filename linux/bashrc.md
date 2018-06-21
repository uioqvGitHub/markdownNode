[TOC]



###.bashrc

```bash
#
# ~/.bashrc
#

# If not running interactively, don't do anything
[[ $- != *i* ]] && return
### 关闭控制台喇叭   禁止响铃
setterm -blength 0
### 控制台提示符
PS1='\e[36;40m[\e[32;40m\H\e[36;40m]-[\e[30;47m\D{%F %H:%M}\e[36;40m]-\e[32;40m\u\e[36;40m-[\e[32;40m\w\e[36;40m]\n\e[36;40m\$\e[0m '

alias ls='ls --color=auto'
alias ..='cd ..'
alias ll='ls -ltr'
alias la='ls -latr'
alias ~='cd ~'
alias doc='cd ~/Documents/workspace'
alias dld='cd ~/Downloads'
alias tomcat='cd /Users/victorhan/Downloads/apache-tomcat-7/apache-tomcat-7.0.52/bin'
alias jhost='ssh jhost'
alias idea='intellij-idea-ultimate-edition'
alias gitl='git log --graph --pretty=oneline'
```



###PS1

其中“F“为字体颜色，编号为30-37，“B”为背景颜色，编号为40-47。颜色表如下

```

F       B
30      40      黑色
31      41      红色
32      42      绿色
33      43      黄色
34      44      蓝色
35      45      紫红色
36      46      青蓝色
37      47      白色
```



```
\a an ASCII bell character (07)
\d the date in "Weekday Month Date" format (e.g., "Tue May 26")
\D{format}
The format is passed to strftime(3) and the result is inserted into the prompt string; an empty format results in a locale-specific time representation. The
Braces are required
\e an ASCII escape character (033)
\h the hostname up to the first `.'
\H the hostname
\j the number of jobs currently managed by the shell
\l the basename of the shell's terminal device name
\n newline
\r carriage return
\s the name of the shell, the basename of $0 (the portion following the final slash)
\t the current time in 24-hour HH:MM:SS format
\T the current time in 12-hour HH:MM:SS format
\@ the current time in 12-hour am/pm format
\A the current time in 24-hour HH:MM format
\u the username of the current user
\v the version of bash (e.g., 2.00)
\V the release of bash, version + patch level (e.g., 2.00.0)
\w the current working directory, with $HOME abbreviated with a tilde (uses the value of the PROMPT_DIRTRIM variable)
\W the basename of the current working directory, with $HOME 
```

```

1878/5000
\a  ASCII铃声字符（07）
\d  星期几多少号
\D {格式}
格式传递给strftime（3），并将结果插入到提示字符串中;空格式会导致特定于语言环境的时间表示。该
大括号是必需的
\ e ASCII转义字符（033）
\ h主机名到第一个'。'
\ H主机名
\ j当前由shell管理的作业数量
\ l shell的终端设备名称的基本名称
\ n换行符
\ r回车
\ shell的名称，$ 0的基名（最后一个斜杠后面的部分）
\ 24小时HH：MM：SS格式的当前时间
\ T当前时间为12小时HH：MM：SS格式
\当前时间以12小时上午/下午格式
A当前时间为24小时HH：MM格式
\ u当前用户的用户名
\ v bash的版本（例如2.00）
\ V bash的发布，版本+补丁级别（例如2.00.0）
\ w当前工作目录，$ HOME用代字号缩写（使用PROMPT_DIRTRIM变量的值）
\ W当前工作目录的基本名称，$ HOME用代字号缩写
\！该命令的历史编号
\＃此命令的命令号
\ $如果有效的UID为0，则为＃，否则为$
```

