#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#ifdef DEBUG
#define DPRINTF(fmt, args...) fprintf(stderr, "%s %s " fmt, _FILE, __FUNCTION_, ##args )
#else
#define DPRINTF(fmt, args...)
#endif

#define MAXDISCO 10
#define QTDPINOS 3

struct Pino
{
    int qtd;
    int discos[MAXDISCO];
};

typedef struct Pino Pino;

static Pino pinos[QTDPINOS];
int count = 0;
int quantidade = 4;

void DesenhaDisco(int tam )
{
    int i;

    for (i = 0; i < 11 - tam; i++)
    {
        printf(" ");
    }

    if (tam == 0)
    {
        printf("|");
    }
    else
    {
        for (i = 0; i < tam << 1; i++)
        {
            if (i == tam)
                printf("|");
            printf("=");
        }
    }

    for (i = 0; i < 11 - tam; i++)
    {
        printf(" ");
    }
}

void MostraPinos()
{
    int i, j, k;

    for (i = quantidade - 1; i >= 0; i--)
    {
        for (j = 0; j < QTDPINOS; j++)
        {

            k = pinos[j].discos[i];

            DesenhaDisco(k);
        }

        printf("\n");
    }
    printf("          (0)                    (1)                    (2)\n");
}

void IniciaPinos()
{
    int i;

    if (quantidade > MAXDISCO)
    {
        printf("Erro: qtd disco invalido\n");
        return;
    }

    memset(pinos, 0, sizeof(Pino) * QTDPINOS);

    for (i = 0; i < quantidade; i++)
    {
        pinos[0].discos[i] = quantidade - i;
        pinos[0].qtd++;
    }
}

int MovePino( int orig, int dest )
{
    DPRINTF("orig=%d dest=%d\n", orig, dest);

    if (pinos[orig].qtd == 0)
    {
        printf("Erro: Não há discos no pino de origem.\n");
        return dest;
    }
    else if (pinos[dest].qtd == 0 || pinos[orig].discos[pinos[orig].qtd - 1] < pinos[dest].discos[pinos[dest].qtd - 1])
    {
        pinos[dest].discos[ pinos[dest].qtd  ] = pinos[orig].discos[ pinos[orig].qtd-1 ];
        pinos[orig].discos[ pinos[orig].qtd-1] = 0;
        pinos[dest].qtd++;
        pinos[orig].qtd--;
        
        printf("Move de %d para %d:\n", orig, dest);
        MostraPinos();
        count++;
    }
    else
    {
        printf("Erro: Movimento inválido. Não é permitido colocar um disco maior sobre um disco menor.\n");
    }

    return dest;
}

void TorreHanoi(int origem, int destino, int auxiliar, int quantidade)
{
    if (quantidade == 1)
    {
        MovePino(origem, destino);
    }
    else
    {
        TorreHanoi(origem, auxiliar, destino, quantidade-1);
        TorreHanoi(origem, destino, auxiliar, 1);
        TorreHanoi(auxiliar, destino, origem, quantidade-1);
    }
}

void LimparTela()
{
    #ifdef _WIN32
    system("cls");
    #else
    system("clear");
    #endif
}


    LimparTela();
    printf("===== Torre de Hanoi =====\n");
    printf("Número de discos: %d\n", quantidade);
    printf("Movimentos realizados: %d\n", count);
    printf("\n");
    MostraPinos();
    printf("\n");
    printf("Escolha uma opção:\n");
    printf("1. Mover disco\n");
    printf("2. Escolher número de discos\n");
    printf("3. Reiniciar jogo\n");
    printf("4. Sair\n");
    printf("Opção: ");
}

void MoverDisco()
{
    int origem, destino;
    char buffer[10];

    while (1)
    {
        ExibirMenu();

        fgets(buffer, sizeof(buffer), stdin);

        switch (buffer[0])
        {
        case '1':
            printf("Digite a origem e o destino separados por espaço (ou 'q' para sair para o menu): ");
            fgets(buffer, sizeof(buffer), stdin);

            if (buffer[0] == 'q' || buffer[0] == 'Q')
                return;

            if (sscanf(buffer, "%d %d", &origem, &destino) != 2)
            {
                printf("Entrada inválida. Tente novamente.\n");
                getchar(); // Limpar o buffer de entrada
                continue;
            }

            if (origem < 0 || origem >= QTDPINOS || destino < 0 || destino >= QTDPINOS)
            {
                printf("Pino(s) inválido(s). Tente novamente.\n");
                continue;
            }

            MovePino(origem, destino);
            break;

        case '2':
            printf("Digite o novo número de discos: ");
            fgets(buffer, sizeof(buffer), stdin);

            if (sscanf(buffer, "%d", &quantidade) != 1 || quantidade <= 0 || quantidade > MAXDISCO)
            {
                printf("Número de discos inválido. Tente novamente.\n");
                getchar(); // Limpar o buffer de entrada
                continue;
            }

            IniciaPinos();
            break;

        case '3':
            IniciaPinos();
            count = 0;
            break;

        case '4':
            printf("Saindo...\n");
            exit(0);

        default:
            printf("Opção inválida. Tente novamente.\n");
            break;
        }
    }
}

int main()
{
    IniciaPinos();

    MoverDisco();

    return 0;
}
