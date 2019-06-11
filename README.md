# Jogo_c#include <stdio.h>
#include <conio.h>
#include <time.h>
#include <windows.h>
int i, a, b, c, player[20][2], direcao, mac;
int h, g, u, pts, tempo, vida = 5;

typedef enum
{
    BLACK,
    

    
    BLUE,
    GREEN,
    CYAN,
    RED,
    MAGENTA,
    BROWN,
    LIGHTGRAY,
    DARKGRAY,
    LIGHTBLUE,
    LIGHTGREEN,
    LIGHTCYAN,
    LIGHTRED,
    LIGHTMAGENTA,
    YELLOW,
    WHITE
} COLORS;

static int __BACKGROUND = BLACK;
static int __FOREGROUND = LIGHTGRAY;

void textcolor(int color)
{
    __FOREGROUND = color;
    SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE),
                            color + (__BACKGROUND << 4));
}
void gotoxy(int x, int y)
{
    COORD c;
    c.X = x;
    c.Y = y;
    SetConsoleCursorPosition(GetStdHandle(STD_OUTPUT_HANDLE), c);
}
int som(int f)
{
    Beep(f, 150); /* primeiro a frquência depois o tempo de duração do som */
    return 0;
}
void desenha_lacraia()
{
    a = player[0][0]; /*pega a posião x do rabo da lacraia*/
    b = player[0][1]; /*pega a posião y do rabo da lacraia*/
    textcolor(LIGHTGREEN);
    for (i = 0; i < 20; i++)
    {                                       /* todos os vinte pedaços da lacraia */
        gotoxy(player[i][0], player[i][1]); /* coloca o cursor na posição */
        printf("%c", 42); /*42*/            /* coloca cada um dos pedaço da lacraia */
    }
    gotoxy(player[19][0], player[19][1]); /* coloca o cursor na posição da cabeça da lacraia */
    printf("O");                          /* imprime a cabeça da lacraia */
    textcolor(BLACK);
    gotoxy(a, b);                 /* coloca o cursor na posição do rabo da lacraia */
    printf(" ");                  /* apaga o último caractere da lacraia que é o rabo que tinha ficado para tras */
    gotoxy(59, 2);                /*coloca o cursor na posição de imprimir os pontos */
    printf("                  "); /* apaga o que estiver escrito */
    gotoxy(59, 2);                /* coloca o cursor na posição de imprimir os pontos */
    textcolor(YELLOW);
    printf("Pontos -%c %d", 16, pts); /* imprime os pontos */
    gotoxy(59, 5);
    printf("Vidas -%c %d", 16, vida);
    tempo++; /* soma o tempo */
    if (tempo > 80)
    {                 /* tempo que a maçã pode ficar no mesmo lugar */
        mac = 0;      /* para poder colocar outra maçã*/
        gotoxy(h, g); /* coloca o cursor na posição da maçã */
        printf(" ");  /* remove a maçã */
        som(1500);    /* som maçã mudou de lugar */
    }
}
void atualiza_lacraia()
{
    for (i = 0; i < 19; i++)
    {                                    /* a lacraia tem vinte pedaços */
        player[i][0] = player[i + 1][0]; /* puxa os pedaços da lcraia para tras */
        player[i][1] = player[i + 1][1]; /* puxa os pedaços da lcraia para tras */
    }
}
int move_lacraia()
{
    for (i = 0; i < 20; i++)
    {
        c = 0;
        if (kbhit())
            c = getch();
        if (c == 27)
            return 0;
        if (c == 77)
        { /* seta p/direita */
            if (direcao == 2)
                som(500); /* tentou voltar para tras */
            else
                direcao = 0;
        }
        if (c == 80)
        { /* seta p/baixo */
            if (direcao == 3)
                som(500); /* tentou voltar para tras */
            else
                direcao = 1;
        }
        if (c == 75)
        { /* seta p/esquerda */
            if (direcao == 0)
                som(500); /* tentou voltar para tras */
            else
                direcao = 2;
        }
        if (c == 72)
        { /* seta p/cima */
            if (direcao == 1)
                som(500); /* tentou voltar para tras */
            else
                direcao = 3;
        }
    }
    switch (direcao)
    {
    case 0:
        if (player[19][0] < 45)
        {                       /* coluna da posição da cabeça da lacraia */
            atualiza_lacraia(); /* puxa os pedaços da lacraia para tras */
            player[19][0]++;    /* move a cabeça da lacraia para direita */
        }
        else
        {
            som(900); /* som bateu nas bordas */
            u = 1;
        }
        break;
    case 1:
        if (player[19][1] < 23)
        {                       /* linha da posição da cabeça da lacraia */
            atualiza_lacraia(); /* puxa os pedaços da lacraia para tras */
            player[19][1]++;    /* move a cabeça da lacraia para baixo */
        }
        else
        {
            som(900); /* som bateu nas bordas */
            u = 1;
        }
        break;
    case 2:
        if (player[19][0] > 1)
        {                       /* coluna da posição da cabeça da lacraia */
            atualiza_lacraia(); /* puxa os pedaços da lacraia para tras */
            player[19][0]--;    /* move a cabeça da lacraia para esquerda */
        }
        else
        {
            som(900); /* som bateu nas bordas */
            u = 1;
        }
        break;
    case 3:
        if (player[19][1] > 1)
        {                       /* linha da posição da cabeça da lacraia */
            atualiza_lacraia(); /* puxa os pedaços da lacraia para tras */
            player[19][1]--;    /* move a cabeça da lacraia para cima */
        }
        else
        {
            som(900); /* som bateu nas bordas */
            u = 1;
        }
        break;
    }
    if (player[19][0] == h && player[19][1] == g)
    {              /* se lacraia comeu a maçã */
        mac = 0;   /* sem maça */
        pts += 3;  /* soma os pontos */
        som(2200); /* som comeu maçã */
    }
    return 0;
}
int coloca_maca()
{
    int t = 0, u = 0;
    if (mac == 0)
        tempo = 0;
    if (mac == 1)
        return 0; /*se já tem maçã não coloca outra*/
    do
    {
        h = rand() % 45 + 1; /*escolhe coluna da maçã*/
        g = rand() % 22 + 1; /*escolhe linh   da maçã*/
        for (i = 0; i < 20; i++)
        {
            if (h == player[i][0] && g == player[i][1]) /*se algum pedaço da lacraia for = a posição da maçã repete*/
                u = 1;                                  /*para repetir caso a maçã esteja sobre a lacraia */
        }
        t++;
        if (t > 1000)
        { /* se o sistema demorar muito para escolher a posição da maçã */
            u = 0;
            g = 1;
            h = 1;
        }
    } while (u != 0);
    gotoxy(h, g); /*coluna e linha da maçã*/
    textcolor(WHITE);
    printf("%c", 3); /* coloca a maçã no lugar */
    mac = 1;         /* informa que já tem uma maçã na tela */
    return 0;
}

void colisao()
{ /* verificar se colidiu com parede ou com ela mesma */
    int h, g;
    h = player[19][0];
    g = player[19][1]; /*pega posicao da cabeca */
    for (i = 0; i < 19; i++)
    {                                               /*verifica se bateu nela mesma */
        if (h == player[i][0] && g == player[i][1]) /*se algum pedaço da lacraia for = a posição da maçã repete*/
            u = 1;                                  /*para repetir caso a maçã esteja sobre a lacraia */
    }
    if (u == 1)
    { /* diminui as vidas da lacraia e posiciona ela na posição inicial */
        vida--;
        direcao = 0;
        sleep(1000);
        for (i = 0; i < 20; i++)
        { /* remove a lacraia da tela */
            gotoxy(player[i][0], player[i][1]);
            printf(" ");
        }
        for (i = 0; i < 20; i++)
        {                         /* coloca a posiçao da lacraia no vetor */
            player[i][0] = i + 5; /* coloca as colunas */
            player[i][1] = 10;    /* coloca as linhas */
        }
    }
}
int main()
{
    srand(time(NULL));
    printf("PRESS ENTER PARA INICIAR ");
    getch();
    system("cls");
    textcolor(LIGHTRED);
    for (i = 0; i < 47; i++)
    { /* desenha as bordas */
        if (i < 24)
        {
            gotoxy(0, i); /* primeira coluna da tela */
            printf("%c", 176);
            gotoxy(46, i);
            printf("%c", 176); /* 219 caractere branco */
        }
        gotoxy(i, 0); /* primeira linha da tela */
        printf("%c", 176);
        gotoxy(i, 24); /* ultima linha da tela */
        printf("%c", 176);
    }
    for (i = 0; i < 20; i++)
    {                         /* coloca a posiçao da lacraia no vetor */
        player[i][0] = i + 5; /* coloca as colunas */
        player[i][1] = 10;    /* coloca as linhas */
    }
    do
    {
        u = 0;
        desenha_lacraia();
        move_lacraia();
        colisao();
        coloca_maca();
        Sleep(200); /* velocidade da lacraia */
        if (vida < 0)
        {
            gotoxy(10, 10);
            printf("GAME OVER");
            c = 27;
        }
    } while (c != 27);
    gotoxy(10, 21);
    return 0;
}
Jogo_c
