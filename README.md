# Import the pygame module
import pygame
from urllib import response
import matplotlib.pyplot as plt
from matplotlib.widgets import Button
import matplotlib.pyplot as plt
import numpy as np
import requests as requests

# function to show the plot

# Import pygame.locals for easier access to key coordinates
# Updated to conform to flake8 and black standards
from pygame.locals import (
    RLEACCEL,
)

# Define constants for the screen width and height
SCREEN_WIDTH = 800
SCREEN_HEIGHT = 600

# Define a player object by extending pygame.sprite.Sprite
# The surface drawn on the screen is now an attribute of 'player'
class Player(pygame.sprite.Sprite):
    def __init__(self):
        super(Player, self).__init__()
        self.surf = pygame.image.load("graph.png").convert()
        self.surf.set_colorkey((255, 255, 255), RLEACCEL)
        self.rect = self.surf.get_rect()

# Initialize pygame
pygame.init()

# Create the screen object
# The size is determined by the constant SCREEN_WIDTH and SCREEN_HEIGHT
screen = pygame.display.set_mode((SCREEN_WIDTH, SCREEN_HEIGHT))

# Instantiate player. Right now, this is just a rectangle.
player = Player()

# Variable to keep the main loop running
running = True

# button class
class Button():
    def __init__(self, x, y, image, scale):
        width = image.get_width()
        height = image.get_height()
        self.image = pygame.transform.scale(image, (int(width * scale), int(height * scale)))
        self.rect = self.image.get_rect()
        self.rect.topleft = (x, y)
        self.clicked = False

    def draw(self, surface):
        action = False
        # get mouse position
        pos = pygame.mouse.get_pos()

        # check mouseover and clicked conditions
        if self.rect.collidepoint(pos):
            if pygame.mouse.get_pressed()[0] == 1 and self.clicked == False:
                self.clicked = True
                action = True

        if pygame.mouse.get_pressed()[0] == 0:
            self.clicked = False

        # draw button on screen
        surface.blit(self.image, (self.rect.x, self.rect.y))

        return action

# create button object

button_img = pygame.image.load('refresh-button.png')
button = Button(650, 100, button_img, 0.1)

# Main loop
def userinput(param):
    pass


while running:
    # for loop through the event queue
    for event in pygame.event.get():
        # check for KEYDOWN event
        if event.type == pygame.KEYDOWN:
            # If the Esc key is pressed, then exit the main loop
            if event.key == pygame.K_ESCAPE:
                running = False

        # check for QUIT event
        elif event.type == pygame.QUIT:
            running = False

    # Fill the screen with black
    screen.fill((255, 255, 255))

    # Draw the player on the screen
    screen.blit(player.surf, (20, 20))

    # Draw the button on the screen
    button.draw(screen)

    # check if button is clicked
    if button.clicked:
        # Extract the temperature and precipitation data for each day
        lat = "51.5085"
        lon = "-0.1257"
        api_key = '8bdc67502b740ebdd020cbbde6f76225'

        url = 'http://api.openweathermap.org/data/2.5/forecast?lat={}&lon={}&appid={}'.format(lat, lon, api_key)
        res = requests.get(url)
        data = res.json()

        print(data)

        date1 = int(data['list'][0]['main']['temp']) - 150
        date2 = int(data['list'][2]['main']['temp']) - 150
        date3 = int(data['list'][3]['main']['temp']) - 150
        date4 = int(data['list'][4]['main']['temp']) - 150
        date5 = int(data['list'][5]['main']['temp']) - 150

        print(date1)

        # Historical LINE
        # x axis values
        x1 = [17, 18, 19, 20, 21]
        y1 = [3.4, 4.2, 3.4, 6.8, 11]

        # plotting the points
        plt.plot(x1, y1, color='green', linestyle='dashed', linewidth=3,
                 marker='o', markerfacecolor='blue', markersize=12)

        # Predicted LINE
        x2 = [17, 18, 19, 20, 21]
        y2 = [date1, date2, date3, date4, date5]

        # plotting the points
        plt.plot(x2, y2, color='red', linestyle='dashed', linewidth=3,
                 marker='o', markerfacecolor='purple', markersize=12)

        # limit decimals from appearing on x axis
        plt.xticks(np.arange(min(x1), max(x1) + 1, 1.0))

        # setting x and y axis range
        plt.ylim(0, 200)
        plt.xlim(17, 21)

        # naming the x axis
        plt.xlabel('Date')
        # naming the y axis
        plt.ylabel('Temperature C')
        # giving the botom of go
        plt.title("")
        # giving a title to my graph
        plt.title('Temp graph for the week')
        plt.title("")
        # save the created figure to a PNG (will overwrite itself)
        plt.savefig('graph.png')


    # Update the display
    pygame.display.flip()

# Quit pygame properly
pygame.quit()
