# Server-chat
2023 | Network Programming Term Project

# Putty를 활용한 서버 내 채팅 기능 구현

## 프로젝트 개요

이 프로젝트는 Putty를 활용하여 서버와 클라이언트 간 실시간 채팅 기능을 구현한 네트워크 프로그램입니다. 여러 클라이언트가 서버에 접속하여 서로 메시지를 주고받을 수 있는 시스템을 구축했습니다. 클라이언트는 고유 ID를 통해 서버에 접속하고, 메시지를 입력 및 수신할 수 있습니다.

## 주요 기능

1. **클라이언트-서버 실시간 채팅**:
   - 클라이언트가 서버에 접속하면 고유 ID를 입력하고, 실시간으로 채팅 메시지를 보낼 수 있습니다.
   - 서버는 각 클라이언트의 메시지를 관리하고, 다른 클라이언트에게 메시지를 전달합니다.

2. **동작 원리**:
   - 클라이언트가 서버에 접속하면 서버에서 클라이언트의 고유 ID를 확인하고 접속 알림을 출력합니다.
   - 클라이언트는 메시지를 입력할 수 있으며, 채팅 상대방에게 메시지 전송 시간과 고유 ID가 함께 출력됩니다.

## 코드 예시

### 클라이언트 코드
```cpp
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <pthread.h>
#include <arpa/inet.h>
#include <time.h>

#define MAX_MESSAGE_LENGTH 1024

void *handle_server_message(void *arg);

int main(int argc, char *argv[]) {
    if (argc != 3) {
        fprintf(stderr, "사용법: %s <서버 IP> <포트 번호>
", argv[0]);
        exit(EXIT_FAILURE);
    }

    char *server_ip = argv[1];
    int port = atoi(argv[2]);

    int client_socket = socket(AF_INET, SOCK_STREAM, 0);
    if (client_socket == -1) {
        perror("소켓 생성 실패");
        exit(EXIT_FAILURE);
    }

    struct sockaddr_in server_address;
    server_address.sin_family = AF_INET;
    server_address.sin_addr.s_addr = inet_addr(server_ip);
    server_address.sin_port = htons(port);

    if (connect(client_socket, (struct sockaddr *)&server_address, sizeof(server_address)) == -1) {
        perror("서버 연결 실패");
        exit(EXIT_FAILURE);
    }

    printf("사용자 이름을 입력하세요: ");
    char client_id[20];
    fgets(client_id, sizeof(client_id), stdin);
    client_id[strcspn(client_id, "
")] = '
