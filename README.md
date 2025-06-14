# Batalha-Naval---RCOS
#include <stdio.h>
#include <stdbool.h> // Para usar o tipo bool

#define TAMANHO_TABULEIRO 10
#define AGUA 0
#define NAVIO 3

// Função para inicializar o tabuleiro com água
void inicializarTabuleiro(int tabuleiro[TAMANHO_TABULEIRO][TAMANHO_TABULEIRO]) {
    for (int i = 0; i < TAMANHO_TABULEIRO; i++) {
        for (int j = 0; j < TAMANHO_TABULEIRO; j++) {
            tabuleiro[i][j] = AGUA;
        }
    }
}

// Função para exibir o tabuleiro
void exibirTabuleiro(int tabuleiro[TAMANHO_TABULEIRO][TAMANHO_TABULEIRO]) {
    printf("  ");
    for (int j = 0; j < TAMANHO_TABULEIRO; j++) {
        printf("%d ", j);
    }
    printf("\n");
    for (int i = 0; i < TAMANHO_TABULEIRO; i++) {
        printf("%d ", i);
        for (int j = 0; j < TAMANHO_TABULEIRO; j++) {
            printf("%d ", tabuleiro[i][j]);
        }
        printf("\n");
    }
}

// Função para validar se uma posição está dentro dos limites do tabuleiro
bool isDentroLimites(int linha, int coluna) {
    return (linha >= 0 && linha < TAMANHO_TABULEIRO && coluna >= 0 && coluna < TAMANHO_TABULEIRO);
}

// Função para validar se um navio pode ser posicionado (não se sobrepõe)
bool podePosicionarNavio(int tabuleiro[TAMANHO_TABULEIRO][TAMANHO_TABULEIRO], int linha, int coluna) {
    return (isDentroLimites(linha, coluna) && tabuleiro[linha][coluna] == AGUA);
}

// Função para posicionar um navio horizontal ou vertical
bool posicionarNavioHV(int tabuleiro[TAMANHO_TABULEIRO][TAMANHO_TABULEIRO], int linhaInicial, int colunaInicial, int tamanho, char orientacao) {
    if (orientacao == 'H' || orientacao == 'h') { // Horizontal
        if (colunaInicial + tamanho > TAMANHO_TABULEIRO) return false; // Ultrapassa limites
        for (int i = 0; i < tamanho; i++) {
            if (!podePosicionarNavio(tabuleiro, linhaInicial, colunaInicial + i)) {
                return false; // Sobreposição ou fora dos limites
            }
        }
        for (int i = 0; i < tamanho; i++) {
            tabuleiro[linhaInicial][colunaInicial + i] = NAVIO;
        }
    } else if (orientacao == 'V' || orientacao == 'v') { // Vertical
        if (linhaInicial + tamanho > TAMANHO_TABULEIRO) return false; // Ultrapassa limites
        for (int i = 0; i < tamanho; i++) {
            if (!podePosicionarNavio(tabuleiro, linhaInicial + i, colunaInicial)) {
                return false; // Sobreposição ou fora dos limites
            }
        }
        for (int i = 0; i < tamanho; i++) {
            tabuleiro[linhaInicial + i][colunaInicial] = NAVIO;
        }
    } else {
        return false; // Orientação inválida
    }
    return true;
}

// Função para posicionar um navio na diagonal
// tipo_diagonal: 1 para diagonal principal (i, i), 2 para diagonal secundária (i, 9-i)
bool posicionarNavioDiagonal(int tabuleiro[TAMANHO_TABULEIRO][TAMANHO_TABULEIRO], int linhaInicial, int colunaInicial, int tamanho, int tipo_diagonal) {
    if (tipo_diagonal == 1) { // Diagonal principal (i, i)
        if (linhaInicial + tamanho > TAMANHO_TABULEIRO || colunaInicial + tamanho > TAMANHO_TABULEIRO) return false;
        for (int i = 0; i < tamanho; i++) {
            if (!podePosicionarNavio(tabuleiro, linhaInicial + i, colunaInicial + i)) {
                return false; // Sobreposição ou fora dos limites
            }
        }
        for (int i = 0; i < tamanho; i++) {
            tabuleiro[linhaInicial + i][colunaInicial + i] = NAVIO;
        }
    } else if (tipo_diagonal == 2) { // Diagonal secundária (i, 9-i)
        if (linhaInicial + tamanho > TAMANHO_TABULEIRO || colunaInicial - (tamanho - 1) < -1) return false; // Ajuste para o limite inferior da coluna
        // Verifica se a diagonal se encaixa na linhaInicial e colunaInicial
        // Ex: para 10x10, (0,9), (1,8), ..., (9,0)
        // Se a linhaInicial é 0 e colunaInicial é 9, o primeiro ponto está ok.
        // Se a linhaInicial é 1 e colunaInicial é 8, o primeiro ponto está ok.
        // Se a linhaInicial é 0 e colunaInicial é 8, o primeiro ponto não está na diagonal secundária
        if (linhaInicial + colunaInicial != TAMANHO_TABULEIRO - 1) { // Verifica se o ponto inicial está na diagonal secundária
             // Encontra o ponto de partida na diagonal secundária mais próximo
            int diff = (linhaInicial + colunaInicial) - (TAMANHO_TABULEIRO - 1);
            linhaInicial -= diff;
            colunaInicial += diff;
            if (!isDentroLimites(linhaInicial, colunaInicial)) return false; // Ponto de partida ajustado fora dos limites
        }
        
        for (int i = 0; i < tamanho; i++) {
            if (!podePosicionarNavio(tabuleiro, linhaInicial + i, colunaInicial - i)) {
                return false; // Sobreposição ou fora dos limites
            }
        }
        for (int i = 0; i < tamanho; i++) {
            tabuleiro[linhaInicial + i][colunaInicial - i] = NAVIO;
        }

    } else {
        return false; // Tipo de diagonal inválido
    }
    return true;
}


int main() {
    int tabuleiro[TAMANHO_TABULEIRO][TAMANHO_TABULEIRO];

    inicializarTabuleiro(tabuleiro);
    printf("Tabuleiro inicial:\n");
    exibirTabuleiro(tabuleiro);

    // Posicionar os dois navios horizontais/verticais
    printf("\nPosicionando navios horizontais/verticais...\n");
    if (posicionarNavioHV(tabuleiro, 0, 0, 3, 'H')) {
        printf("Navio H (0,0) de tamanho 3 posicionado com sucesso.\n");
    } else {
        printf("Falha ao posicionar navio H (0,0) de tamanho 3.\n");
    }

    if (posicionarNavioHV(tabuleiro, 4, 5, 2, 'V')) {
        printf("Navio V (4,5) de tamanho 2 posicionado com sucesso.\n");
    } else {
        printf("Falha ao posicionar navio V (4,5) de tamanho 2.\n");
    }

    // Posicionar os dois navios diagonais
    printf("\nPosicionando navios diagonais...\n");
    // Navio na diagonal principal (i, i)
    if (posicionarNavioDiagonal(tabuleiro, 1, 1, 4, 1)) {
        printf("Navio Diagonal Principal (1,1) de tamanho 4 posicionado com sucesso.\n");
    } else {
        printf("Falha ao posicionar navio Diagonal Principal (1,1) de tamanho 4.\n");
    }

    // Navio na diagonal secundária (i, 9-i)
    if (posicionarNavioDiagonal(tabuleiro, 0, 9, 3, 2)) { // Começando em (0,9), vai para (1,8), (2,7)
        printf("Navio Diagonal Secundária (0,9) de tamanho 3 posicionado com sucesso.\n");
    } else {
        printf("Falha ao posicionar navio Diagonal Secundária (0,9) de tamanho 3.\n");
    }
    
    // Tentativa de sobreposição para demonstrar validação
    printf("\nTentando posicionar navio em uma posição já ocupada (deve falhar):\n");
    if (posicionarNavioHV(tabuleiro, 0, 1, 2, 'H')) {
        printf("Navio H (0,1) de tamanho 2 posicionado com sucesso (NÃO DEVERIA ACONTECER).\n");
    } else {
        printf("Falha ao posicionar navio H (0,1) de tamanho 2 (EXPECTED).\n");
    }

    printf("\nTabuleiro final:\n");
    exibirTabuleiro(tabuleiro);

    return 0;
}
