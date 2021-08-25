---
title: Восстановление ответа
---

Теперь мы умеем находить ответ в задачах на ДП, но в некоторых задачах
нам также интересно как мы можем его получить, например в задаче про
черепашку нам может быть интересен путь. Такую задачу называют
восстановлением ответа в динамике.

Есть два способа, которыми можно это сделать.

1\) Хранить в массив prev откуда ты пришел в эту клетку.

Когда мы выбираем максимум из левой и верхней клетки, мы на самом деле
решаем, какой последний ход будет в оптимальном пути до этой клетки -
сверху или слева, и берем ответ для той клетки, сложнный с монетами в
этой клетке. Давайте координаты клетки, откуда мы пришли, хранить в
массиве prev. Или, в данном случае, можно хранить не координаты а
просто 1, если пришли слева, и 0, если пришли сверху.

``` C++ numberLines
for (int i = 0; i < n; i++) {
    for (int j = 0; j < m; j++) {
        if (i == 0 && j == 0) {
            dp[0][0] = COINS[0][0];
            prev[0][0] = -1;
        }
        else if (i == 0) {
            dp[0][j] = dp[0][j - 1] + COINS[0][j];
            prev[0][j] = 0;
        }
        else if (j == 0) {
            dp[i][0] = dp[i - 1][0] + COINS[i][0];
            prev[i][0] = 1;
        }
        else {
            dp[i][j] = max(dp[i - 1][j], dp[i][j - 1]) + COINS[i][j];
            if (dp[i - 1][j] > dp[i][j - 1]) {
                prev[i][j] = 1;
            }
            else {
                prev[i][j] = 0;
            }
        }
    }
}
```

И, чтобы восстановить ответ, надо просто пройтись с конца по этим
клеткам до самого начала, и развернуть получившуюся
последовательность.

``` C++ numberLines
while (i > 0 || j > 0) {
    if (prev[i][j] == 1) {
        i -= 1;
        answer_directions.push_back("DOWN");
    }
    else {
        j -= 1;
        answer_directions.push_back("RIGHT");
    }
    answer.push_back({i, j});
}
reverse(answer.begin(), answer.end());
reverse(answer_directions.begin(), answer_directions.end());
```

2\) Вместо хранения массива prev догадаться по массиву dp, откуда именно
черепашка пришла в эту клетку.

В данном примере это довольно легко. Если мы уже посчитали весь массив
dp, то теперь можно начиная с конца легко понять, пришла черепашка туда
сверху или слева в оптимальном маршруте - она пришла из клетки с
максимальным числом монет.

``` C++ numberLines
while (i > 0 || j > 0) {
    if (i != 0 && (j == 0 || dp[i - 1][j] > dp[i][j - 1])) {
        i -= 1;
        answer_directions.push_back("DOWN");
    }
    else {
        j -= 1;
        answer_directions.push_back("RIGHT");
    }
    answer.push_back({i, j})
}
reverse(answer.begin(), answer.end());
reverse(answer_directions.begin(), answer_directions.end());
```