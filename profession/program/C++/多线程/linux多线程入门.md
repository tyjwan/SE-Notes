# C Linux 多线程入门
×××
## 入门示例
&ensp;&ensp;&ensp;&ensp;启动和停止直接使用接口即可，暂停和恢复需要自己定义，大体的代码如下：

```c
#include <stdio.h>
#include <string.h>
#include <stdlib.h>
#include <pthread.h>
#include <unistd.h>
#include <signal.h>
#include <sys/time.h>

struct sigaction newact, oldact;
unsigned int unslept;

// 自定义信号处理
void tm_sig_alrm(int signo) {
    /* nothing to do */
    sigset_t siguser1_mask, siguser2_mask,oldmask;
    sigemptyset(&siguser1_mask);
    sigemptyset(&siguser2_mask);
    sigaddset(&siguser1_mask, SIGUSR1);
    sigaddset(&siguser2_mask, SIGUSR2);

    switch (signo)
    {
        case SIGUSR1:
            pause();
            break;
        case SIGUSR2:
            break;
        default:
            return;
    }
}

// 暂停线程
int tm_thread_suspend(pthread_t tid) {
    return pthread_kill(tid, SIGUSR1);
}

// 恢复线程
int tm_thread_resume(pthread_t tid) {
    return pthread_kill(tid, SIGUSR2);
}

// 循环输出
void task() {
	int count = 0;
	while(1) {
		printf("count: %d\n", count);
		sleep(1);
	}
}

int main(int argc, char const *argv[])
{
	newact.sa_handler = tm_sig_alrm; //注册了 SIGALRM 信号的处理函数 sig_alrm
    sigemptyset(&newact.sa_mask);
    newact.sa_flags = 0;
    sigaction(SIGUSR1, &newact, &oldact);
    sigaction(SIGUSR2, &newact, &oldact);

    pthread_t threadId;
    // 启动线程
    pthread_create(&threadId, NULL, task, NULL);
    sleep(10);
    // 暂停线程
    tm_thread_suspend(threadId);
    sleep(10);
    // 恢复线程
    tm_thread_resume(threadId);
    sleep(10);
    // 停止线程
    pthread_cancel(threadId);

	return 0;
}
```