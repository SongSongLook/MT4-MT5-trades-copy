// MT4 Signal Sender & Receiver with TCP
#property strict
#include <stderror.mqh>
#include <stdlib.mqh>

input bool IsSender = true;      // 是否為發送端
input bool IsReceiver = false;  // 是否為接收端
input double Multiplier = 1.0;  // 手數倍率
input string IPAddress = "127.0.0.1"; // TCP 目標 IP
input int Port = 8888;           // TCP 目標埠號

int socketHandle = INVALID_HANDLE;

int OnInit() {
    if (IsSender) Print("MT4 發送端啟動");
    if (IsReceiver) Print("MT4 接收端啟動");

    // 初始化 TCP 套接字
    socketHandle = SocketCreate();
    if (socketHandle == INVALID_HANDLE) {
        Print("無法創建套接字: ", GetLastError());
        return(INIT_FAILED);
    }

    if (IsSender) {
        if (!SocketConnect(socketHandle, IPAddress, Port)) {
            Print("無法連接到接收端: ", GetLastError());
            return(INIT_FAILED);
        }
    }

    if (IsReceiver) {
        if (!SocketListen(socketHandle, Port)) {
            Print("無法監聽埠號: ", GetLastError());
            return(INIT_FAILED);
        }
    }

    return(INIT_SUCCEEDED);
}

void OnTick() {
    if (IsSender) SendSignal();
    if (IsReceiver) ReceiveSignal();
}

void SendSignal() {
    for (int i = OrdersTotal() - 1; i >= 0; i--) {
        if (OrderSelect(i, SELECT_BY_POS, MODE_TRADES)) {
            static int lastTicket = -1;
            if (OrderTicket() != lastTicket) {
                lastTicket = OrderTicket();

                // 構建數據
                string data = OrderSymbol() + "," + DoubleToString(OrderLots(), 2) + "," +
                              DoubleToString(OrderOpenPrice(), 5) + "," +
                              DoubleToString(OrderStopLoss(), 5) + "," +
                              DoubleToString(OrderTakeProfit(), 5);

                // 發送數據
                int sent = SocketSend(socketHandle, data);
                if (sent <= 0) {
                    Print("信號發送失敗: ", GetLastError());
                } else {
                    Print("信號已發送: ", data);
                }
            }
        }
    }
}

void ReceiveSignal() {
    char buffer[256];
    int received = SocketReceive(socketHandle, buffer, sizeof(buffer) - 1);
    if (received > 0) {
        buffer[received] = '\0'; // Null-terminate the string
        string data = buffer;

        // 解析數據
        string symbol;
        double lots, openPrice, stopLoss, takeProfit;
        int res = StringSplit(data, ',', symbol, lots, openPrice, stopLoss, takeProfit);
        if (res > 1) {
            lots *= Multiplier; // 調整手數
            if (SymbolSelect(symbol, true)) {
                int ticket = OrderSend(symbol, OP_BUY, lots, openPrice, 10, stopLoss, takeProfit);
                if (ticket < 0) {
                    Print("下單失敗: ", GetLastError());
                } else {
                    Print("成功下單: ", ticket);
                }
            }
        }
    }
}

