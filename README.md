# 🃏 Milestone Project 2: Card Game

## Description

This project implements a simple card game in Python, where the player competes against the dealer to get as close to 21 points without exceeding it. The game supports card shuffling, drawing, and handling Ace values dynamically.

## Features

- 🂠 **Card Class**: Represents a single card with a suit, rank, and value.
- 🃏 **Deck Class**: Manages a full deck of cards, including shuffling and drawing.
- 👤 **Player Class**: Handles player actions, including adding and removing cards, and calculating the total value of the cards in hand.
- 🎮 **Gameplay Functions**: Includes functions for player decisions, such as choosing the value of an Ace and whether to continue the game.

## Code Overview

```python
import random

suits = ('Hearts', 'Diamonds', 'Spades', 'Clubs')
ranks = ('Two', 'Three', 'Four', 'Five', 'Six', 'Seven', 'Eight', 'Nine', 'Ten', 'Jack', 'Queen', 'King', 'Ace')
values = {'Two': 2, 'Three': 3, 'Four': 4, 'Five': 5, 'Six': 6, 'Seven': 7, 'Eight': 8, 
    'Nine': 9, 'Ten': 10, 'Jack': 10, 'Queen': 10, 'King': 10, 'Ace': 11}

class Card:
    def __init__(self, suit, rank):
        self.suit = suit
        self.rank = rank
        self.value = values[rank]

    def __str__(self):
        return f"{self.rank} of {self.suit}"
    
    def set_ace_value(self, new_value):
        self.value = new_value

    def get_name_of_card(self):
        return f"{self.rank} of {self.suit}"

class Deck:
    def __init__(self):
        self.all_cards = [Card(suit, rank) for suit in suits for rank in ranks]

    def shuffle(self):
        random.shuffle(self.all_cards)
        
    def take_one_card(self):
        return self.all_cards.pop(0)

class Player:
    def __init__(self, name):
        self.all_card = []
        self.name = name
        self.cards_value = 0

    def add_card(self, card):
        if card.rank == "Ace":
            ace_value = ask_for_ace_value(self.name)
            card.set_ace_value(ace_value)
        self.all_card.append(card)

    def remove_all_cards(self):
        self.all_card = []

    def get_value_of_all_cards(self):
        return sum(card.value for card in self.all_card)

    def __str__(self):
        return f"{self.name} has {self.get_value_of_all_cards()} points"

def ask_for_ace_value(name):
    while True:
        try:
            ace_value = int(input(f"{name}, choose the value of the Ace, 1 or 11: "))
            if ace_value in [1, 11]:
                return ace_value
            else:
                print(f"{name}, you need to provide 1 or 11")
        except ValueError:
            print(f"{name}, you need to provide 1 or 11")

def player_move(name):
    while True:
        try:
            player_input = int(input(f"{name}, choose 1 to take card and 2 to pass: "))
            if player_input in [1, 2]:
                return player_input
            else:
                print(f"{name}, you need to provide 1 or 2")
        except ValueError:
            print(f"{name}, you need to provide 1 or 2")

def if_continue_game(name):
    while True:
        try:
            player_input = int(input(f"{name}, choose 1 to continue game and 2 to end: "))
            if player_input in [1, 2]:
                return player_input == 1
            else:
                print(f"{name}, you need to provide 1 or 2")
        except ValueError:
            print(f"{name}, you need to provide 1 or 2")

game_on = True
player_name = input("Enter your name: ")
player = Player(player_name)
player_cash = 150

while game_on and player_cash > 0:
    deck_of_cards = Deck()
    deck_of_cards.shuffle()
    player.remove_all_cards()

    dealer = Player("Dealer")

    for _ in range(2):
        dealer.add_card(deck_of_cards.take_one_card())
        player.add_card(deck_of_cards.take_one_card())

    print(f"Dealer's first card: {dealer.all_card[0].get_name_of_card()} ({dealer.all_card[0].value} points)")
    
    print(f"\n{player.name}'s cards:")
    for card in player.all_card:
        print(f"{card.get_name_of_card()} ({card.value} points)")
    print(f"Total: {player.get_value_of_all_cards()} points\n")

    if player_move(player.name) == 1:
        dealer.add_card(deck_of_cards.take_one_card())
        player.add_card(deck_of_cards.take_one_card())
    else:
        dealer.add_card(deck_of_cards.take_one_card())

    print(f"Dealer's cards:")
    for card in dealer.all_card:
        print(f"{card.get_name_of_card()} ({card.value} points)")
    print(f"Total: {dealer.get_value_of_all_cards()} points\n")

    print(f"{player.name}'s cards:")
    for card in player.all_card:
        print(f"{card.get_name_of_card()} ({card.value} points)")
    print(f"Total: {player.get_value_of_all_cards()} points\n")

    if dealer.get_value_of_all_cards() > 21 and player.get_value_of_all_cards() <= 21:
        print("You won!")
        player_cash += 100
    elif dealer.get_value_of_all_cards() <= 21 and player.get_value_of_all_cards() > 21:
        print("You lost!")
        player_cash -= 100
    elif dealer.get_value_of_all_cards() <= 21 and player.get_value_of_all_cards() <= 21:
        if dealer.get_value_of_all_cards() == player.get_value_of_all_cards():
            print("Draw!")
        elif dealer.get_value_of_all_cards() > player.get_value_of_all_cards():
            print("You lost!")
            player_cash -= 100
        else:
            print("You won!")
            player_cash += 100

    print(f"You have {player_cash} $")
    game_on = if_continue_game(player.name)

print("End of game")
print(f"Your score is {player_cash} $")
