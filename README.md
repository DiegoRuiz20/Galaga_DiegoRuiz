import pygame, random
import os

WIDTH = 800
HEIGHT = 600
BLACK = (0, 0, 0)
WHITE = (255, 255, 255)
GREEN = (0, 255, 0)
ROOT_DIR = os.path.dirname(__file__)
IMAGE_DIR = os.path.join(ROOT_DIR, 'assets')

pygame.init()
pygame.mixer.init()
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("GALAGA")
clock = pygame.time.Clock()

def draw_text(surface, text, size, x, y):
    font = pygame.font.SysFont("serif", size)
    text_surface = font.render(text, True, WHITE)
    text_rect = text_surface.get_rect()
    text_rect.midtop = (x, y)
    surface.blit(text_surface, text_rect)

def draw_shield_bar (surface, x, y, percentage):
    BAR_LENGTH = 100
    BAR_HEIGTH =  10
    fill = (percentage / 100 ) - BAR_LENGTH
    border = pygame.Rect(x, y, BAR_LENGTH, BAR_HEIGTH)
    fill = pygame.Rect(x, y, fill, BAR_HEIGTH)
    pygame.draw.rect(surface, GREEN, fill)
    pygame.draw.rect(surface, WHITE, border, 2)
    
class Player(pygame.sprite.Sprite):
    def __init__(self):
        super().__init__()
        self.image = pygame.image.load(os.path.join(IMAGE_DIR,"player.png")).convert()
        self.image.set_colorkey(BLACK)
        self.rect = self.image.get_rect()
        self.rect.centerx = WIDTH // 2
        self.rect.botton = HEIGHT - 10
        self.speed_x = 0
        self.shield = 100
        
    def update(self):
        self.speed_x = 0
        keystate = pygame.key.get_pressed()
        if keystate[pygame.K_LEFT]:
            self.speed_X = -5
        if keystate[pygame.K_RIGHT]:
            self.speed_x = 5
        self.rect.x += self.speed_x
        if self.rect.right > WIDTH:
            self.rect.right = WIDTH
        if self.rect.left < 0:
            self.rect.left = 0
            
            
    def shoot(self):
        bullet = Bullet(self.rect.centerx, self.rect.top)
        all_sprites.add(bullet)
        bullets.add(bullet)
        laser_sound.play()
                    
                
    
class Meteor(pygame.sprite.Sprite):
    def __init__(self, *groups):
        super().__init__()
        self.image = random.choice(meteor_images)
        self.image.set_colorkey(BLACK)
        self.rect = self.image.get_rect()
        self.rect.x = random.randrange(WIDTH - self.rect.width)
        self.rect.y = random.randrange(-140, -100)
        self.speedy = random.randrange(1, 10)
        self.speedx = random.randrange(-5, 5)


    def update(self):
        self.rect.y += self.speedy
        self.rect.x += self.speedx
        if self.rect.top > HEIGHT + 10 or self.rect.left < -40 or self.rect.right > WIDTH +40:
            self.rect.x = random.randrange(WIDTH - self.rect.width)
            self.rect.x =random(-140, - 100)
            self.rect.y =random(1, 10) 
            
class Bullet(pygame.sprite.Sprite):
    def __init__(self, x, y):
        super().__init__()
        self.image = pygame.image.load(os.path.join(IMAGE_DIR,"laser1.png"))
        self.image.set_colorkey(BLACK)
        self.rect = self.image.get_rect()
        self.rect.y = y
        self.rect.centerx = x
        self.speedy = -10
        
        
        def update(self):
            self.rect.y += self.speedy
            if self.rect.bottom < 0:
                self.kill()
                
class Explosion(pygame.sprite.Sprite):
    def __init__(self, center):
        super(). __init__()
        self.image = explosion_anim[0]
        self.rect = self.image.get_rect()
        self.rect.center = center
        self.frame = 0
        self.last_updte = pygame.time.get_ticks()
        self.frame_rate = 50 
        
        
        
    def update(self):
        now = pygame.time.get_ticks()
        if now - self.last_update > self.frame_rate:
            self.last_updte = now
            self.frame += 1
            if self.frame == len(explosion_anim):
                self.kill()
            else:
                center = self.rect.center
                self.image = explosion_anim[self.frame]
                self.rect = self.image.get_rect()
                self.rect.center = center
                
def show_go_screen():
    screen.blit(background, [0,0])
    draw_text(screen, "SHOOTER", 65, WIDTH // 2, HEIGHT // 4)
    draw_text(screen, "Intruciones van aqui", 27 , WIDTH // 2, HEIGHT // 2)
    draw_text(screen, "Press key", 20, WIDTH // 2, HEIGHT * 3 / 4)
    pygame.display.flip()
    waiting = True
    while waiting:
        clock.tick(60)
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
            if event.type == pygame.KEYUP:
                waiting = False


Meteor_images = []
meteor_list = ["meterorGrey_big1.png", "meterorGrey_big2.png","meterorGrey_big3.png", "meterorGrey_big4.png",
               "meterorGrey_med1.png", "meterorGrey_med2.png", "meterorGrey_small.png", "meterorGrey_small.png",
               "meterorGrey_tiny1.png", "meterorGrey_tiny2.png"]

for img in meteor_list:
    meteor_images.append(pygame.image.image.load(os.path.join(IMAGE_DIR, img)).convert())
