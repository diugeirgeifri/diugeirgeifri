from pygame import *
from random import randint
win_w = 700
win_h = 500
score = 0
lost = 0
mixer.init()
mixer.music.load('fire.ogg')
mixer.music.play()
# fire_s = mixer.Sound('fire.mp3')
class GameSprite(sprite.Sprite):
    def __init__(self, name, x, y, speed, w=50, h=50):
        super().__init__()
        self.image = transform.scale(image.load(name), (w,h))
        self.speed = speed
        self.rect = self.image.get_rect()
        self.rect.x = x
        self.rect.y = y
        self.w = w
        self.h = h
    def reset(self):
        window.blit(self.image, (self.rect.x, self.rect.y))
class Player(GameSprite):
    def update(self):  
        keys = key.get_pressed()
        if keys[K_LEFT] and self.rect.x > 5:
            self.rect.x -= self.speed
        if keys[K_RIGHT] and self.rect.x < win_w - self.w:
            self.rect.x += self.speed
    def fire(self):
        bul = Bulit('bullet.png', self.rect.centerx, self.rect.top, 8, 60,100)
        buls.add(bul)
class Enemy(GameSprite):
    def update(self):
        self.rect.y += self.speed
        global lost
        if self.rect.y > win_h:
            lost += 1
            self.rect.y = 0
            self.rect.x = randint(50, win_h-self.h)
class Bulit(GameSprite):
    def update(self):
        self.rect.y -=self.speed
        if self.rect.y < 0:
            self.kill()
window = display.set_mode((win_w,win_h))
back = transform.scale(image.load('ufo.png'), (win_w,win_h))
player = Player('rocket.png', 350, 420, 5, 80,80)
monsters = sprite.Group()
buls = sprite.Group()
for i in range(3):
    monster = Enemy('asteroid.png', randint(80, win_w - 80), 0, randint(1,3),50, 50)
    monsters.add(monster)
font.init()
font1 = font.SysFont('Arial',120)
font2 = font.SysFont('Arial',40)
win = font1.render('Победа', True, (255,220,0))
lose = font1.render('СЛил', True, (255,0,0))

clock =  time.Clock()
run = True
finish = False
while run:
    for i in event.get():
        if i.type == QUIT:
            run = False
        elif i.type == KEYDOWN:
            if i.key == K_SPACE:
                player.fire()
    if finish != True:
        text_score = font2.render('Счет: ' + str(score),True,(0,0,0))
        text_lost = font2.render('Пропущено: ' + str(lost),True,(255,255,255))
        window.blit(back, (0,0))
        player.update()
        monsters.update()
        buls.update()
        player.reset()
        monsters.draw(window)
        buls.draw(window)
        window.blit(text_score, (10,10))
        window.blit(text_lost, (10,40))

        collides = sprite.groupcollide(monsters, buls, True, True)
        for c in collides:
            score += 1
            monster = Enemy('ufo.png', randint(80, win_w - 80), 0, randint(1,3),50, 50)
            monsters.add(monster)

        if sprite.spritecollide(player, monsters, False) or lost >=9:
            finish = True
            window.blit(lose, (200, 200))
            
    display.update()
    clock.tick(60)


