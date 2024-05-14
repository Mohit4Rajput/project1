# SPACE INVADER GAME - INTRODUCTION TO PYTHON PROGGRAMMING - COURSE PROJECT 
#inspired from space invader


import math
import pygame
import random

from pygame import mixer

# intialize pygame
pygame.init()
# screen
screen = pygame.display.set_mode(((800, 600)))
# title
pygame.display.set_caption("War in the clouds")
icon = pygame.image.load('alien.png')
pygame.display.set_icon(icon)
# background
background = pygame.image.load('background.png.jpg')

# music
mixer.music.load("Wartime - AShamaluevMusic (1).mp3")
mixer.music.play(-1)

# shooter
vacineimg = pygame.image.load('airplane.png')
vacinex = 370
vaciney = 530
vacinex_change = 0
# covidarmy
covidimg = []
covidx = []
covidy = []
covidy_change = []
covidx_change = []
covidcount = 9
for i in range(covidcount):
    covidimg.append(pygame.image.load('ufo.png'))
    covidx.append(random.randint(0, 735))
    covidy.append(random.randint(50, 200))
    covidy_change.append(20)
    covidx_change.append(0.3)

# cure
# rready == you cant see the cure in the clouds
# fire the cure is moving in the clouds
cureimg = pygame.image.load('bullet.png')
curex = 0
curey = 530
curey_change = 2
cure_state = "ready"

# score
score_val = 0
fonttype = pygame.font.Font('freesansbold.ttf', 32)
scorex = 350
scorey = 10

# GAMEOVER_TEXT
gameover_font = pygame.font.Font('freesansbold.ttf', 64)


def game_over_text():
    gameover_text = gameover_font.render("!!!GAME OVER!!!", True, (255, 0, 255))
    screen.blit(gameover_text, (250, 300))


def scoreboard(x, y):
    score = fonttype.render("Score:" + str(score_val), True, (0, 0, 0))
    screen.blit(score, (x, y))


def doctor(x, y):
    screen.blit(vacineimg, (x, y))


def covid(x, y, i):
    screen.blit(covidimg[i], (x, y))


def fire_cure(x, y):
    global cure_state
    cure_state = "fire"
    screen.blit(cureimg, (x + 15, y + 9))


def kills(covidx, covidy, curex, curey):
    d = math.sqrt((math.pow(covidx - curex, 2)) + (math.pow(covidy - curey, 2)))
    if d < 28:
        return True
    else:
        return False


# loop1
run = True
while run:

    # rgb
    screen.fill((244, 194, 194))
    # background image
    screen.blit(background, (0, 0))

    for eve in pygame.event.get():
        if eve.type == pygame.QUIT:
            run = False
        # IF KEY IS PRESSED CHECK
        if eve.type == pygame.KEYDOWN:
            if eve.key == pygame.K_LEFT:
                vacinex_change -= 0.9
                print("left arrow pressed")
            if eve.key == pygame.K_RIGHT:
                vacinex_change += 0.9
                print("right arrow pressed")
            if eve.key == pygame.K_SPACE:
                if cure_state is "ready":
                    fire_sound = mixer.Sound("Missile+3.wav")
                    fire_sound.play()
                    # get the x cordinate of vaccine
                    curex = vacinex
                    fire_cure(curex, curey)

        if eve.type == pygame.KEYUP:
            if eve.key == pygame.K_LEFT or eve.type == pygame.K_RIGHT:
                vacinex_change = 0
                print("key released")
    # boundaries check
    vacinex += vacinex_change
    if vacinex <= 0:
        vacinex = 0
    elif vacinex >= 768:
        vacinex = 768
    # covid movement

    for i in range(covidcount):

        # gameover
        if covidy[i] > 400:
            for j in range(covidcount):
                covidy[j] = 2000
                defeated_sound = mixer.Sound("fail-buzzer-03 (1).wav")
                defeated_sound.play()

            game_over_text()
            break

        covidx[i] += covidx_change[i]
        if covidx[i] <= 0:
            covidx_change[i] = 0.5
            covidy[i] += covidy_change[i]

        elif covidx[i] >= 768:
            covidx_change[i] = -0.5
            covidy[i] += covidy_change[i]

            # colision
        collision = kills(covidx[i], covidy[i], curex, curey)
        if collision:
            blast_sound = mixer.Sound("Gun+357+Magnum.wav")
            blast_sound.play()
            curey = 530
            cure_state = "ready"
            score_val += 1
            print(score_val)
            covidx[i] = random.randint(0, 735)
            covidy[i] = random.randint(50, 200)
        covid(covidx[i], covidy[i], i)

        # cure movement
    if curey <= 0:
        curey = 530
        cure_state = "ready"

    if cure_state is "fire":
        fire_cure(curex, curey)
        curey -= curey_change

    doctor(vacinex, vaciney)
    scoreboard(scorex, scorey)

    pygame.display.update()



