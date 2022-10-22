# Jogoo
#Projeto de Programação
#Jogo de obstaculos
#fazer score do jogo em %

#https://www.youtube.com/watch?v=HW41UNolUec 
import pygame 
import time
from pygame.locals import *
from random import randrange, choice

pygame.init()

BLACK = ( 0, 0, 0)
WHITE = (255, 255, 255)
RED = (255, 0, 0)
GREEN = ( 0, 255, 0)
BLUE = ( 0, 0, 255)
SILVER = (192,192,192)
PURPLE = (128,0,128)
janela_largura = 500
janela_altura = 400

janela = pygame.display.set_mode((janela_largura,janela_altura))
pygame.display.set_caption("Jogo")
fundo_jogo = pygame.image.load(r'C:\Users\bia21\OneDrive - Instituto Maua de Tecnologia\4º ANO\Prog. Orientada a Objetos\Jogo\FUNDO_AZUL.jpg')  
personagem_sprite = pygame.image.load(r'C:\Users\bia21\OneDrive - Instituto Maua de Tecnologia\4º ANO\Prog. Orientada a Objetos\Jogo\personagem1.png').convert_alpha()
chao_sprite = pygame.image.load(r'C:\Users\bia21\OneDrive - Instituto Maua de Tecnologia\4º ANO\Prog. Orientada a Objetos\Jogo\CHAO.jpg')
obstaculo1_sprite = pygame.image.load(r'C:\Users\bia21\OneDrive - Instituto Maua de Tecnologia\4º ANO\Prog. Orientada a Objetos\Jogo\plant.png')
obstaculo2_sprite = pygame.image.load(r'C:\Users\bia21\OneDrive - Instituto Maua de Tecnologia\4º ANO\Prog. Orientada a Objetos\Jogo\plant2.png')
obstaculo3_sprite = pygame.image.load(r'C:\Users\bia21\OneDrive - Instituto Maua de Tecnologia\4º ANO\Prog. Orientada a Objetos\Jogo\obstaculo_chao.png')
bird_sprite = pygame.image.load(r'C:\Users\bia21\OneDrive - Instituto Maua de Tecnologia\4º ANO\Prog. Orientada a Objetos\Jogo\bird.png')

clock = pygame.time.Clock()
sorteio_obstaculo = choice([0,1,2]) 

class personagem(pygame.sprite.Sprite):
    def __init__(self):
        pygame.sprite.Sprite.__init__(self)
        self.imagens_personagem = []    #lista que armazena os frames da sprite sheet
        self.pos_lista = 0 #variavel posicao lista

        for i in range(3):  #posicao dos 3 frames da sprite seet
            img = personagem_sprite.subsurface((i * 48,0), (48,50)) #seleciona a imagem no tamanho estabelecido
            img = pygame.transform.scale(img, (48*1.3, 50*1.3)) #aumenta o tamanho da imagem
            self.imagens_personagem.append(img)   #adciona os frames na lista 

        self.image = self.imagens_personagem[self.pos_lista]
        self.rect = self.image.get_rect() #borda do frame (retangulo)
        self.pos_y_inicial = 350-50 #subtrair a posiçao desejada da altura do frame
        self.rect.y = 350-50
        self.rect.center = [120,350-50]  #posiciona o retangulo no frame em uma posicao da tela 
        self.mask = pygame.mask.from_surface(self.image) #cria uma marcara da superficie -> para colisão
        self.pulo = False

    def pular(self):
        self.pulo = True

    def plataforma(self):
        self.plataforma = True

    def update(self):
        if self.pulo == True:
            if self.rect.y <=200: #limita ate onde o personagem sobe
                self.pulo = False
            self.rect.y -= 18 #personagem sobe tantos pixels no eixo y
        else: 
            if self.rect.y < self.pos_y_inicial:
                self.rect.y += 15
            else:
                self.rect.y = self.pos_y_inicial #faz o personagem voltar para o 'chao' 
        
        if self.pos_lista > 2:
            self.pos_lista = 0
        self.pos_lista += 0.5 #incrementa a variavel para ir mudando os frames
        self.image = self.imagens_personagem[int(self.pos_lista)]
        
        if self.plataforma == True:
            self.rect.y = self.pos_y_inicial

class chao(pygame.sprite.Sprite):
    def __init__(self, pos_x):
        pygame.sprite.Sprite.__init__(self)
        self.image = chao_sprite.subsurface((0,0), (350,67)) #seleciona a imagem no tamanho estabelecido 
        self.rect = self.image.get_rect() #atribui a imagem como um retangulo 
        self.rect.y = 350
        self.rect.x = pos_x * 350

    def update(self):
        if self.rect.topright[0] < 0: #posição do canto direito superior do retangulo for menor que 0
            self.rect.x = janela_largura #lado direito
        self.rect.x -=9

class obstaculo1(pygame.sprite.Sprite):
    def __init__(self):
        pygame.sprite.Sprite.__init__(self)
        self.image = obstaculo1_sprite.subsurface((0,64), (64,64)) #seleciona a imagem no tamanho estabelecido 
        #self.image = pygame.transform.scale(self.image, (64*0.5, 94*0.5))
        self.rect = self.image.get_rect()
        self.rect.x = janela_largura
        self.rect.y = 300
        self.mask = pygame.mask.from_surface(self.image)
        self.sorteio = sorteio_obstaculo

    def update(self):
        if self.sorteio == 0:
            if self.rect.topright[0] < 0: #posição do canto direito superior do retangulo for menor que 0
                self.rect.x = janela_largura #lado direito
            self.rect.x -=9

class obstaculo2(pygame.sprite.Sprite):
    def __init__(self):
        pygame.sprite.Sprite.__init__(self)
        self.image = obstaculo2_sprite.subsurface((63,0), (63,90)) #seleciona a imagem no tamanho estabelecido 
        self.image = pygame.transform.scale(self.image, (63*0.6, 90*0.6))
        self.rect = self.image.get_rect()
        self.rect.x = janela_largura
        self.rect.y = 300
        self.mask = pygame.mask.from_surface(self.image)
        self.sorteio = sorteio_obstaculo

    def update(self):
        if self.sorteio == 1:
            if self.rect.topright[0] < 0: #posição do canto direito superior do retangulo for menor que 0
                self.rect.x = janela_largura #lado direito
            self.rect.x -=9

class obstaculo3(pygame.sprite.Sprite):
    def __init__(self):
        pygame.sprite.Sprite.__init__(self)
        self.image = obstaculo3_sprite.subsurface((0,0), (93,5)) #seleciona a imagem no tamanho estabelecido 
        self.image = pygame.transform.scale(self.image, (93*1.3, 5*1.3))
        self.rect = self.image.get_rect()
        self.rect.x = janela_largura
        self.rect.y = 281
        self.mask = pygame.mask.from_surface(self.image)
        self.sorteio = sorteio_obstaculo
    
    def update(self):
        if self.sorteio == 3:
            if self.rect.topright[0] < 0: #posição do canto direito superior do retangulo for menor que 0
                self.rect.x = janela_largura #lado direito
            self.rect.x -=9

class obstaculo31(pygame.sprite.Sprite):
    def __init__(self):
        pygame.sprite.Sprite.__init__(self)
        self.image = obstaculo3_sprite.subsurface((0,6), (93,6)) #seleciona a imagem no tamanho estabelecido 
        self.image = pygame.transform.scale(self.image, (93*1.3, 6*1.3))
        self.rect = self.image.get_rect()
        self.rect.x = janela_largura
        self.rect.y = 289
        self.mask = pygame.mask.from_surface(self.image)
        self.sorteio = sorteio_obstaculo

    def update(self):
        if self.sorteio == 3:
            if self.rect.topright[0] < 0: #posição do canto direito superior do retangulo for menor que 0
                self.rect.x = janela_largura #lado direito
            self.rect.x -=9

class bird(pygame.sprite.Sprite):
    def __init__(self):
        pygame.sprite.Sprite.__init__(self)
        self.imagens_personagem = []    #lista que armazena os frames da sprite sheet
        for i in range(3):  #posicao dos frames da sprite seet
            img = bird_sprite.subsurface((i * 32,0), (32,32)) #seleciona a imagem no tamanho estabelecido
            img = pygame.transform.scale(img, (32*1.2, 32*1.2)) #aumenta o tamanho da imagem
            self.imagens_personagem.append(img)   #adciona os frames na lista 
        self.pos_lista = 0
        self.image = self.imagens_personagem[self.pos_lista]
        self.rect = self.image.get_rect() #borda do frame (retangulo)
        self.rect.center = [janela_largura,250]  #posiciona o retangulo no frame em uma posicao da tela 
        self.rect.x = janela_largura
        self.mask = pygame.mask.from_surface(self.image)
        self.sorteio = sorteio_obstaculo
        
    def update(self):
        if self.sorteio == 2:
            if self.rect.topright[0] < 0: #posição do canto direito superior do retangulo for menor que 0
                self.rect.x = janela_largura #lado direito
            self.rect.x -=9
        if self.pos_lista > 2:
            self.pos_lista = 0
        self.pos_lista += 0.25 #incrementa a variavel para ir mudando os frames
        self.image = self.imagens_personagem[int(self.pos_lista)]
        

def Nome(text,x, y, T, C):
    fonte = pygame.font.Font('freesansbold.ttf',T)
    texto = fonte.render(text, True, C)
    textoRect = texto.get_rect()
    textoRect.center = ((x),(y))
    janela.blit(texto, textoRect)

def Botao(msg,x,y,a,b,C1,C2):
    mouse = pygame.mouse.get_pos()
    click = pygame.mouse.get_pressed()
    if x+a> mouse[0] > x and y+b > mouse[1] > y:
        pygame.draw.rect(janela, C1,(x,y,a,b))
        if click[0] == 1:
            game()
    else:
        pygame.draw.rect(janela, C2,(x,y,a,b))
    Nome(msg,x+a/2,y+b/2,18, BLACK)

          
def game():
    sprites = pygame.sprite.Group()
    obstaculos = pygame.sprite.Group()
    plataformas = pygame.sprite.Group()

    fox = personagem()
    sprites.add(fox) 

    for i in range(janela_largura):
        solo = chao(i)
        sprites.add(solo)

    arbusto = obstaculo1()
    sprites.add(arbusto) 
    obstaculos.add(arbusto)

    cacto = obstaculo2()
    sprites.add(cacto) 
    obstaculos.add(cacto)

    passaro = bird()
    sprites.add(passaro)
    obstaculos.add(passaro)

    plataforma = obstaculo3()
    sprites.add(plataforma)
    plataformas.add(plataforma)

    plataforma2 = obstaculo31()
    sprites.add(plataforma2)
    obstaculos.add(plataforma2)

    janela_aberta = True
    while janela_aberta:

        clock.tick(20)
        janela.blit(fundo_jogo, (0, 0))
        #fechar janela
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                exit()
        #pular
            if event.type == KEYDOWN:
                if event.key == K_SPACE:
                    if fox.rect.y != fox.pos_y_inicial:
                        pass #não faz nada
                    else:
                        fox.pular()
        #colisao
        crash = pygame.sprite.spritecollide(fox, obstaculos, False, pygame.sprite.collide_mask) #lista que amarzena os obstaculos colididos  
        crash2 = pygame.sprite.spritecollide(fox, plataformas, False, pygame.sprite.collide_mask)
        sprites.draw(janela)  #desenhando as sprites na tela 
        if crash:
            pass
        else:
            sprites.update()
        if crash2:
            fox.rect.y = 230 
            if fox.rect.y == plataforma.rect.y:
                fox.plataforma()

        if cacto.rect.topright[0] <= 0 or arbusto.rect.topright[0] <= 0 or passaro.rect.topright[0] <= 0 or plataforma.rect.topright[0] <= 0 or plataforma2.rect.topright[0] <= 0:
            sorteio_obstaculo = choice([0,1,2,3])
            cacto.rect.x = janela_largura  #repocisionar o objeto no lado direito da dela 
            arbusto.rect.x = janela_largura
            passaro.rect.x = janela_largura
            plataforma.rect.x = janela_largura
            plataforma2.rect.x = janela_largura
            cacto.sorteio = sorteio_obstaculo  #atribuir as variaveis de sorteio, garantir alternancia 
            arbusto.sorteio = sorteio_obstaculo
            passaro.sorteio = sorteio_obstaculo
            plataforma.sorteio = sorteio_obstaculo
            plataforma2.sorteio = sorteio_obstaculo

        pygame.display.flip()  #atualiza a tela


janela_aberta = True
while janela_aberta: 
    clock.tick(20) #20 frames por segundo

    #fundo da tela 
    janela.blit(fundo_jogo, (0, 0))
    janela.blit(chao_sprite, (0, 350))
    fox = personagem_sprite.subsurface((0,0), (48,50))
    fox = pygame.transform.scale(fox, (48*1.3, 50*1.3))
    janela.blit(fox, (120, 300))

    Nome("Fox Crash",janela_largura/2,janela_altura/3,40, GREEN)
    Botao("Jogar",janela_largura/2-50,janela_altura/2,100,30,SILVER,WHITE)
    #fechar janela
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            pygame.quit()
            exit()
        pygame.display.update()
