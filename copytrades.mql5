// MT5 Signal Sender & Receiver with TCP
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
    if (IsSender) Print("MT5 發送端啟動");
    if (IsReceiver) Print("MT5 接收端啟動");

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
    for (int i = PositionsTotal() - 1; i >= 0; i--) {
        ulong ticket = PositionGetTicket(i);
        if (ticket > 0) {
            static ulong lastTicket = 0;
            if (ticket != lastTicket) {
                lastTicket = ticket;

                // 構建數據
                string data = PositionGetString(POSITION_SYMBOL) + "," +
                              DoubleToString(PositionGetDouble(POSITION_VOLUME), 2) + "," +
                              DoubleToString(PositionGetDouble(POSITION_PRICE_OPEN), 5) + "," +
                              DoubleToString(PositionGetDouble(POSITION_SL), 5) + "," +
                              DoubleToString(PositionGetDouble(POSITION_TP), 5);

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
                MqlTradeRequest request;
                MqlTradeResult result;

                request.action = TRADE_ACTION_DEAL;
                request.symbol = symbol;
                request.volume = lots;
                request.type = ORDER_TYPE_BUY;
                request.price = openPrice;
                request.sl = stopLoss;
                request.tp = takeProfit;
                request.deviation = 10;

                if (!OrderSend(request, result)) {
                    Print("下單失敗: ", GetLastError());
                } else {
                    Print("成功下單: ", result.order);
                }
            }
        }
    }
}

