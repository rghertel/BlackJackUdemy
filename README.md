# BlackJackUdemy
blackjack milestone 2 project for Python Bootcamp

import random

suits = ('Hearts', 'Diamonds', 'Spades', 'Clubs')
ranks = ('Two', 'Three', 'Four', 'Five', 'Six', 'Seven', 'Eight', 'Nine', 'Ten', 'Jack', 'Queen', 'King', 'Ace')
values = {'Two':2, 'Three':3, 'Four':4, 'Five':5, 'Six':6, 'Seven':7, 'Eight':8, 
            'Nine':9, 'Ten':10, 'Jack':10, 'Queen':10, 'King':10, 'Ace':11}

class Card:
    
    def __init__(self, suit, rank):
        self.suit = suit
        self.rank = rank
        
    def __str__(self):
        return f'{self.rank} of {self.suit}'

class Deck:
    
    def __init__(self):
        self.all_cards = []
        for suit in suits:
            for rank in ranks:
                create_card = Card(suit, rank)
                self.all_cards.append(create_card)
    
    def __str__(self):
        card_deck = ''
        for card in self.all_cards:
            card_deck += card.__str__() + ', '
        return card_deck
        
    def shuffle(self):
        random.shuffle(self.all_cards)
        
    def deal_one(self):
        return self.all_cards.pop()

class Hand:
    def __init__(self):
        self.cards = []  # start with an empty list as we did in the Deck class
        self.value = 0   # start with zero value
        self.aces = 0    # add an attribute to keep track of aces
    
    def __str__(self):
        view_cards = ''
        for card in self.cards:
            view_cards += card.__str__() + ','
        return view_cards
    
    def add_card(self,card):
        self.cards.append(card)
        self.value += values[card.rank]
        if card.rank == 'Ace':
            self.aces += 1
            
    def adjust_for_ace(self):
        if self.value > 21 and self.aces > 0:
            self.value -= 10
            self.aces -= 1 #Why do we need to subtract an ace? Isn't subtracting 

class Chips:
    
    def __init__(self):
        self.total = 100  # This can be set to a default value or supplied by a user input
        self.bet = 0
        
    def win_bet(self):
        self.total += self.bet*2
    
    def lose_bet(self):
        self.total -= self.bet

def take_bet(chips):
    
    while True:
        try:
            chips.bet = int(input('Place a bet: '))
    
        except:
            print('That is not a correct amount!')
            continue
        
        else:   
            if chips.bet > chips.total:
                print (f"You don't have enough chips to bet {chips.bet}.")
                continue
            
            if chips.bet <= chips.total:
                print (f'You bet {chips.bet}.')
                break

def hit(deck,hand):
    
    hand.add_card(deck.deal_one())
    hand.adjust_for_ace()
    #print (hand)

def hit_or_stand(deck,hand):
    global playing  # to control an upcoming while loop
    
    while True:
        x = input('would you like to "hit" or "stand"?: ')
        
        if x.lower() == 'hit':
            print("You've been dealt a card.")
            hit(deck,hand)
        
        elif x.lower() == 'stand':
            print("You're standing. It's dealer's turn.")
            playing = False
            #break
        else:
            print("That isn't an option. Please try again.")
            continue
        break

def show_some(player,dealer):
    print("Dealer's Hand:")
    print("<card hidden>")
    print(dealer.cards[1])
    print('\n')
    print("Player's Hand:")
    for card in player.cards:
        print(card)
    
def show_all(player,dealer):
    print("Dealer's Hand:")
    for card in dealer.cards:
        print(card)
    print(dealer.value)
    print('\n')
    print("Player's Hand:")
    for card in player.cards:
        print(card)
    print(player.value)

def player_busts(player, dealer, chips):
    print('Player Busts!')
    chips.lose_bet()

def player_wins(player, dealer, chips):
    print('Player Wins!')
    chips.win_bet()
    
def dealer_busts(player, dealer, chips):
    print('Dealer Busts!')
    chips.win_bet()
    
def dealer_wins(player, dealer, chips):
    print('Dealer Wins!')
    chips.lose_bet()
    
def push(player, dealer):
    print("Dealer and Player tie! It's a push.")





playing = True

while True:
    print('Welcome to Black Jack!')

    
    # Create & shuffle the deck, deal two cards to each player
    game_deck = Deck()
    game_deck.shuffle()
    
    players_hand = Hand()
    players_hand.add_card(game_deck.deal_one())
    players_hand.add_card(game_deck.deal_one())
    
    dealers_hand = Hand()
    dealers_hand.add_card(game_deck.deal_one())
    dealers_hand.add_card(game_deck.deal_one())
    
        
    # Set up the Player's chips
    players_chips = Chips()
    
    # Prompt the Player for their bet
    take_bet(players_chips)

    
    # Show cards (but keep one dealer card hidden)
    show_some(players_hand, dealers_hand)

    
    while playing:  # recall this variable from our hit_or_stand function
        
        # Prompt for Player to Hit or Stand
        hit_or_stand(game_deck, players_hand)
        
        # Show cards (but keep one dealer card hidden)
        show_some(players_hand, dealers_hand) 
        
        # If player's hand exceeds 21, run player_busts() and break out of loop
        if players_hand.value > 21:
            player_busts(players_hand, dealers_hand, players_chips)
            break

    # If Player hasn't busted, play Dealer's hand until Dealer reaches 17
    if players_hand.value <= 21:
            
        while dealers_hand.value < 17:
            hit(game_deck, dealers_hand)
                
        # Show all cards
        show_all(players_hand, dealers_hand)
    
        # Run different winning scenarios
        if dealers_hand.value > 21:
            dealer_busts(players_hand, dealers_hand, players_chips)

        elif players_hand.value > dealers_hand.value:
            player_wins(players_hand, dealers_hand, players_chips)
            
        elif players_hand.value < dealers_hand.value:
            dealer_wins(players_hand, dealers_hand, players_chips)
            
        elif players_hand.value == dealers_hand.value:
            push(players_hand, dealers_hand)
    
    # Inform Player of their chips total 
    print(f"Player's total chips: {players_chips.total}")
    
    # Ask to play again
    play_again = input('Would you like to play again? Enter "yes" or "no".')
        
    if play_again.lower() == 'yes':
        playing = True
    
    else:
        print('Thanks for playing Black Jack!')
        playing = False
        break
        
   # else:
       # print("That isn't an option. Please try again.")
       # continue
