# Space-Intruders-working-
import pygame
import random

pygame.init()
pygame.display.set_caption("space intruders!") # window title
screen = pygame.display.set_mode((800, 800)) #creates game screen
clock = pygame.time.Clock() #clock
gameover = False
#player variables
xpos = 400
ypos = 750
moveLeft = False
moveRight = False
#game variables
timer = 0;
shoot = False
#------------------------------------------------------------------------------------------------
class Intruder:
    def __init__(self, xpos, ypos):
        self.xpos = xpos
        self.ypos = ypos
        self.isAlive = True
        self.direction = 1
    def draw(self):
        if self.isAlive:
            pygame.draw.rect(screen, (250, 250, 250), (self.xpos, self.ypos, 40, 40))
    def move(self, time):
        #reset what direction you're moving every 8 moves:
        if time % 800 == 0:
            self.ypos += 100 #move down
            self.direction *=-1 #flip direction 
            return 0 #resets timer to 0
        
        #move every time the timer increses by 100:
        if timer% 100 == 0:
            self.xpos+=50*self.direction #move right
            
        return time #doesn't reset if first if statement hasn't executed
         
    #Intruder Collision
    def collide(self, BulletX, BulletY):
        if self.isAlive: #only hit live intruders
            if BulletX > self.xpos: #check if bullet is right of the left side of the intruder
                 if BulletX < self.xpos + 40: #check if the bulet is left of the right side
                     if BulletY < self.ypos + 40: #check if the bullet  is above the alien's bottom
                         if BulletY > self.ypos: #check if the bullet is below the top of the alien
                             print("hit") #for testing
                             self.isAlive = False #set the intruder to dead
                             return False #set the Bullet to dead
                            
        return True #otherwise keep bullet alive
armada = [] #create empty list
for i in range (5): #handles rows
    for j in range (6): #handles columns
        armada.append(Intruder(j*60+50, i*50+50))
#------------------------------------------------------------------------------------------------
class Bullet:
    def __init__(self, xpos, ypos):
        self.xpos = xpos
        self.ypos = ypos
        self.isAlive = False
        
    def move(self, xpos, ypos):
        if self.isAlive== True: #only shoot live bullets
            self.ypos-= 5 #move up when shot
        if self.ypos < 0: #check if you've hit the top of the screen
            self.isAlive = False #set to dead
            self.xpos = xpos #reset to player position
            self.ypos = ypos
            
        def draw(self):
            pygame.draw.rect(screen, (250, 250, 250), (self.xpos, self,ypos, 3, 20))
            
#instantiate bullet object
bullet = Bullet(xpos+28, ypos) #create bullet object and pass player position
#------------------------------------------------------------------------------------------------   
class Missile:
    def __init__(self,xpos, ypos):
        self.xpos = -10
        self.ypos = -10
        self.isAlive = False
        
    def move(self, xpos, ypos):
    
        if self.isAlive == True: #only shoot live missiles
            self.ypos-=-5 #move up when shot
            #print("I'm at", self.xpos, self.ypos)
        if self.ypos > 800: #check if you've hit the bottom of the screen
            self.isAlive = False #set to dead
            #print("IM DEAD")
            self.xpos = xpos #reset to player position
            self.ypos = ypos
            
    def draw(self):
        #print("trying to draw missile!")
        if self.isAlive == True:
            
            pygame.draw.rect(screen, (250, 250, 250), (self.xpos, self.ypos, 3, 20))
            #print("trying to draw a missile!")
    

missilebag = [] #create empty list
for i in range (10000):
    missilebag.append(Missile(2, 1))
#for i in range (5): #handles rows
        #armada.append(Intruder(i*60+50, i*50+50))
#------------------------------------------------------------------------------------------------
class Wall:
    def __init__(self, xpos, ypos):
        self.xpos = xpos
        self.ypos = ypos
        self.numHits = 0
    def draw(self):
        if self.numHits ==0:
            pygame.draw.rect(screen,(250, 250, 20), (self.xpos, self.ypos, 30, 30))
        if self.numHits ==1:
            pygame.draw.rect(screen,(150, 150, 10), (self.xpos, self.ypos, 30, 30))
        if self.numHits ==2:
            pygame.draw.rect(screen,(50, 50, 0), (self.xpos, self.ypos, 30, 30))
            
    #copy/paste the alien colliion function here and make the tweaks from the slide
    def collide(self, BulletX, BulletY):
        if self.numHits < 3: #only hit live intruders
            if BulletX > self.xpos: #check if bullet is right of the left side of the intruder
                 if BulletX < self.xpos + 40: #check if the bulet is left of the right side
                     if BulletY < self.xpos + 40: #check if the bullet  is above the alien's bottom
                         if BulletY > self.xpos: #check if the bullet is below the top of the alien
                             print("hit") #for testing
                             self.numHits += 1 #set the intruder to dead
                             return False #set the Bullet to dead
                            
        return True #otherwise keep bullet alive
            
walls = [] #create empty list
for k in range (4): #creates 4 sets
    for i in range (2): #handles rows
        for j in range (3): #handles columns
            walls.append(Wall(j*30+200*k+50, i*30+600)) #push wall objects into list
            
#------------------------------------------------------------------------------------------------
while not gameover: #game loop
    clock.tick(60) #fps
    timer +=1
    #Input Section---------------------------------------------------------------
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            gameover = True #quit game if x is pressed in the top corner
            
    if event.type == pygame.KEYDOWN:
        if event.key == pygame.K_LEFT:
            moveLeft = True
            
    elif event.type == pygame.KEYUP:
        if event.key == pygame.K_LEFT:
            moveLeft = False
            
    if event.type == pygame.KEYDOWN:
        if event.key == pygame.K_RIGHT:
            moveRight = True
            
    elif event.type == pygame.KEYUP:
        if event.key == pygame.K_RIGHT:
            moveRight = False
            
    if event.type == pygame.KEYDOWN:
        if event.key == pygame.K_UP:
            bullet.isAlive = True
            
    elif event.type == pygame.KEYUP:
        if event.key == pygame.K_UP:
            bullet.isAlive = False
            
    # physics section------------------------------------------------------------
    
    #check variables from the input section
    if moveLeft == True:
        vx =- 3
    elif moveRight == True:
        vx =- -3
    else:
        vx = 0
    
    for i in range (len(armada)):
        timer = armada[i].move(timer)
        
        #shoot bullet
        if shoot == True: #check keyboard input
            bullet.isAlive = True
            
        if bullet.isAlive == True:
            bullet.move(xpos+28, ypos) #shoot from player position
            if bullet.isAlive == True:
            #check for collision between bullet and enemy
                for i in range (len(armada)): #check bullet with entire armada'as positions
                    bullet.isAlive = armada[i].collide(bullet.xpos, bullet.ypos) #if we hit, set bullet to false
                    if bullet.isAlive == False:
                        break
                    
            #shoot walls
                    
            if bullet.isAlive == True:
            #check for collision between bullet and enemy
                for i in range (len(walls)): #check bullet with entire list of wall positions
                    bullet.isAlive = walls[i].collide(bullet.xpos, bullet.ypos)
                    if bullet.isAlive == False:
                        break
        
        else: #make bullet follow player when not moving up
            bullet.xpos = xpos + 28
            bullet.ypos = ypos 
    
    #this moves all the missiles in the missile bag
    for i in range (len(missilebag)):
        missilebag[i].move(missilebag[i].xpos,missilebag[i].ypos)
        
    #this randomly shoots the missiles from the aliens    
    chance = random.randrange(100)
    if chance < 2:
        pick = random.randrange(len(armada))
        if armada[pick].isAlive == True:
            for i in range(len(missilebag)):
                if missilebag[i].isAlive == False:
                    missilebag[i].isAlive = True
                    #print("setting a missile to alive!")
                    missilebag[i].xpos = armada[pick].xpos+5
                    missilebag[i].ypos = armada[pick].ypos
                    break
                
    #update player position
    xpos += vx

    #check for wall/missile collision
    for i in range(len(walls)): #check each wall box
        for j in range(len(missilebag)): #against each missile
            if missilebag[j].isAlive == True: #check if missile is true
                if walls[i].collide(missilebag[j].xpos, missilebag[j].ypos) == False:
                    missilebag[j].isAlive = False #kill missile
                    break #stop killing walls if you're dead
    
    #Missile Collision
    for i in range (len(missilebag)):
        if missilebag[i].isAlive:
            missilebag[i].move(xpos, ypos)
            if missilebag[i].xpos > xpos:
               if missilebag[i].xpos < xpos + 40:
                    if missilebag[i].ypos > ypos:
                        print("player hit!")
                        gameover = True
                
                #TO DO SLIDE 39
    #RENDER Section--------------------------------------------------------------
    screen.fill((0,0,0)) #wipes down screenfor you to keep it nice a clean :)
    a1 = Intruder(400, 400) #instantiante intruder object
    #a1 = Bullet(400, 400)
    #a1.draw() #draw object
    
    #draw all funny intruders
    for i in range (len(armada)):
        armada[i].draw()
        
    for i in range (len(walls)):
        walls[i].draw()
        
    for i in range (len(missilebag)):
        missilebag[i].draw()
    
    pygame.draw.rect(screen, (200, 200, 100), (xpos, 750, 60, 20))
    
    pygame.draw.rect(screen, (200, 200, 100), (bullet.xpos, bullet.ypos, 3, 20))
    
    pygame.display.flip() #flips the buffer (memory) where stuff has been "drawn" to the screen
    
#end game loop
    
pygame.quit
