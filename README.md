import pygame
import time
import random

# Inicializando o Pygame
pygame.init()

# Definir cores
preto = (0, 0, 0)
branco = (255, 255, 255)
verde = (0, 255, 0)
vermelho = (213, 50, 80)
azul = (50, 153, 213)

# Definir o tamanho da tela
largura = 600
altura = 400

# Criar a tela
tela = pygame.display.set_mode((largura, altura))
pygame.display.set_caption("Jogo da Cobrinha")

# Definir o relógio (controle de FPS)
relogio = pygame.time.Clock()

# Definir o tamanho do bloco da cobrinha e a velocidade
tamanho_bloco = 10
velocidade = 15

# Definir fontes
fonte = pygame.font.SysFont("bahnschrift", 25)
fonte_pontos = pygame.font.SysFont("comicsansms", 35)

# Função para mostrar mensagem
def mostrar_mensagem(msg, cor):
    mensagem = fonte.render(msg, True, cor)
    tela.blit(mensagem, [largura / 6, altura / 3])

# Função para desenhar o placar
def mostrar_placar(pontos):
    valor = fonte_pontos.render("Pontuação: " + str(pontos), True, branco)
    tela.blit(valor, [0, 0])

# Função principal do jogo
def jogo():
    fim_de_jogo = False
    game_over = False

    # Posições iniciais da cobrinha
    x1 = largura / 2
    y1 = altura / 2

    # Direção da movimentação da cobrinha
    x1_mover = 0
    y1_mover = 0

    # Lista para armazenar os segmentos da cobrinha
    corpo_cobrinha = []
    comprimento_cobrinha = 1

    # Posições iniciais da comida
    comida_x = round(random.randrange(0, largura - tamanho_bloco) / 10.0) * 10.0
    comida_y = round(random.randrange(0, altura - tamanho_bloco) / 10.0) * 10.0

    # Pontuação inicial
    pontos = 0

    while not fim_de_jogo:

        while game_over:
            tela.fill(azul)
            mostrar_mensagem("Você perdeu! Pressione Q para sair ou C para jogar novamente", vermelho)
            mostrar_placar(pontos)
            pygame.display.update()

            for evento in pygame.event.get():
                if evento.type == pygame.KEYDOWN:
                    if evento.key == pygame.K_q:
                        fim_de_jogo = True
                        game_over = False
                    if evento.key == pygame.K_c:
                        jogo()

        for evento in pygame.event.get():
            if evento.type == pygame.QUIT:
                fim_de_jogo = True
            if evento.type == pygame.KEYDOWN:
                if evento.key == pygame.K_LEFT:
                    x1_mover = -tamanho_bloco
                    y1_mover = 0
                elif evento.key == pygame.K_RIGHT:
                    x1_mover = tamanho_bloco
                    y1_mover = 0
                elif evento.key == pygame.K_UP:
                    y1_mover = -tamanho_bloco
                    x1_mover = 0
                elif evento.key == pygame.K_DOWN:
                    y1_mover = tamanho_bloco
                    x1_mover = 0

        # Verificar se a cobrinha bateu nas bordas
        if x1 >= largura or x1 < 0 or y1 >= altura or y1 < 0:
            game_over = True

        # Atualizar posição da cobrinha
        x1 += x1_mover
        y1 += y1_mover
        tela.fill(azul)

        # Desenhar a comida
        pygame.draw.rect(tela, verde, [comida_x, comida_y, tamanho_bloco, tamanho_bloco])

        # Adicionar a nova posição da cobrinha à lista de segmentos
        corpo_cobrinha.append([x1, y1])

        # Se a cobrinha passou do comprimento, removemos o segmento mais antigo
        if len(corpo_cobrinha) > comprimento_cobrinha:
            del corpo_cobrinha[0]

        # Verificar se a cobrinha colidiu com ela mesma
        for segmento in corpo_cobrinha[:-1]:
            if segmento == [x1, y1]:
                game_over = True

        # Desenhar a cobrinha
        for segmento in corpo_cobrinha:
            pygame.draw.rect(tela, branco, [segmento[0], segmento[1], tamanho_bloco, tamanho_bloco])

        # Atualizar a tela
        mostrar_placar(pontos)
        pygame.display.update()

        # Verificar se a cobrinha comeu a comida
        if x1 == comida_x and y1 == comida_y:
            comida_x = round(random.randrange(0, largura - tamanho_bloco) / 10.0) * 10.0
            comida_y = round(random.randrange(0, altura - tamanho_bloco) / 10.0) * 10.0
            comprimento_cobrinha += 1
            pontos += 1

        # Controlar a velocidade do jogo
        relogio.tick(velocidade)

    # Finalizar o Pygame
    pygame.quit()
    quit()

# Rodar o jogo
jogo()
