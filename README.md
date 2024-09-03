import pygame
import random

# Inicializar Pygame
pygame.init()

# Configuración de la pantalla
ANCHO = 800
ALTO = 600
pantalla = pygame.display.set_mode((ANCHO, ALTO))
pygame.display.set_caption("Juego de Motos")

# Colores
BLANCO = (255, 255, 255)
NEGRO = (0, 0, 0)

# Cargar imágenes
moto_img = pygame.image.load("moto.png")
fondo_img = pygame.image.load("fondo.png")

# Clase de la moto
class Moto(pygame.sprite.Sprite):
    def __init__(self):
        super().__init__()
        self.image = moto_img
        self.rect = self.image.get_rect()
        self.rect.center = (ANCHO // 2, ALTO - 100)
        self.velocidad_x = 0

    def update(self):
        self.rect.x += self.velocidad_x
        if self.rect.left < 0:
            self.rect.left = 0
        if self.rect.right > ANCHO:
            self.rect.right = ANCHO

# Clase de los obstáculos
class Obstaculo(pygame.sprite.Sprite):
    def __init__(self):
        super().__init__()
        self.image = pygame.Surface((50, 50))
        self.image.fill(NEGRO)
        self.rect = self.image.get_rect()
        self.rect.x = random.randrange(ANCHO - self.rect.width)
        self.rect.y = random.randrange(-100, -40)
        self.velocidad_y = random.randrange(1, 8)

    def update(self):
        self.rect.y += self.velocidad_y
        if self.rect.top > ALTO:
            self.rect.x = random.randrange(ANCHO - self.rect.width)
            self.rect.y = random.randrange(-100, -40)
            self.velocidad_y = random.randrange(1, 8)

# Crear grupos de sprites
todos_los_sprites = pygame.sprite.Group()
obstaculos = pygame.sprite.Group()

# Crear la moto
moto = Moto()
todos_los_sprites.add(moto)

# Crear obstáculos
for i in range(8):
    obstaculo = Obstaculo()
    todos_los_sprites.add(obstaculo)
    obstaculos.add(obstaculo)

# Bucle principal del juego
corriendo = True
reloj = pygame.time.Clock()

while corriendo:
    # Mantener el bucle a la velocidad correcta
    reloj.tick(60)

    # Procesar eventos
    for evento in pygame.event.get():
        if evento.type == pygame.QUIT:
            corriendo = False
        elif evento.type == pygame.KEYDOWN:
            if evento.key == pygame.K_LEFT:
                moto.velocidad_x = -5
            elif evento.key == pygame.K_RIGHT:
                moto.velocidad_x = 5
        elif evento.type == pygame.KEYUP:
            if evento.key == pygame.K_LEFT or evento.key == pygame.K_RIGHT:
                moto.velocidad_x = 0

    # Actualizar
    todos_los_sprites.update()

    # Comprobar colisiones
    if pygame.sprite.spritecollideany(moto, obstaculos):
        corriendo = False

    # Dibujar / renderizar
    pantalla.blit(fondo_img, (0, 0))
    todos_los_sprites.draw(pantalla)

    # Después de dibujar todo, actualizar la pantalla
    pygame.display.flip()

pygame.quit()
