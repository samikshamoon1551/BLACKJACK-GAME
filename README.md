# BLACKJACK-GAME
import random
##creating super class
class Deck():
    deck_cards = ['A', 'K', 'Q', 'J', '10', '9', '8', '7', '6', '5', '4', '3', '2'] * 4
    #a function that creates a list of cards chosen at random from above list
    def picks(self, num):
        pickd=[]
        for _ in range(num):

            choosen=random.randint(0, len(self.deck_cards) - 1)
            pickd.append(self.deck_cards[choosen])


            self.deck_cards.pop(choosen)

        return pickd
#a class associated with the player
class Player(Deck):

    cards=[]
    bust=False
    def __init__(self):
        self.money = 500

#class associated with the dealer
class Dealr(Deck):

    cards=[]
    bust=False

#definitions
#structure that prints the card
def print_cards(cards):


    for i in range(len(cards)):
        print("{0:^4}-----".format(''))
        print("{0:^4}|{1:^3}|".format('', cards[i]))
        print("{0:^4}-----".format(''))

#prints the game board for gameplay
def print_board(cards1, cards2):


    print("\n"*5)
    print("*************")
    print("Player cards:")
    print("*************")
    print_cards(cards1)
    print("*************")
    print("Dealer cards:")
    print("*************")
    print_cards(cards2)
    print("*************")


#assigninig the values to the different cards in a deck and retrieving the sum of cards with the player and the dealer
def fidvalue(cards):
    card_value=[]
    total=0
    ace=False
    dict={'A':11,'K':10,'Q':10,'J':10,'10':10,'9':9,'8':8,'7':7,'6':6,'5':5,'4':4,'3':3,'2':2}

    for i in range(len(cards)):
        card_value.append(dict[cards[i]])
        if cards[i]=='A':
            ace=True
    for i in range(len(card_value)):
        total +=card_value[i]
    if total>21 and ace:
        total-=10
    return total
#game starts
#defining two empty lists for player and dealer one each
p1= Player()
d1=Dealr()
#two cards with face up in the player's list while one card face up in dealer's list
while True:
    p1.cards=p1.picks(2)
    d1.cards=d1.picks(1)

    dealr_won = False
    #printing on the game board
    print_board(p1.cards, d1.cards)

    while True:
            #player enters the amount of money he wants to bet on
            try:
                bet=int(input("the amount of money  to bet: "))
                if bet> p1.money:
                    print("You dont have sufficient funds!")
                    print("Your account balance is: {}".format(p1.money))
                    raise Exception
                break
            except Exception as e:
                print("Please enter a valid amount!")
    p1.money -=bet                #amount of money left with the player after betting for the round


    while True:
        #player choses whether to hit or stand
        choice = input("hit or stand? [h/s]: ")
        #on chosing to hit one more card is printed on the board
        if choice == 'h':
            p1.cards +=p1.picks(1)
            print_board(p1.cards, d1.cards)
        else:

            break
        #if the sum of values on player cards exceeds 21 he's busted
        if fidvalue(p1.cards) > 21:
            p1.bust =True
            dealr_won =True
            break
        #if the sum is 21 and also one card is an ace then hes a blackjack and immediately wins the game
        elif fidvalue(p1.cards) == 21 and "A" in p1.cards:

            print("player blackjack")
            exit()
            p1.bust=False
            dealr_won=False
            p1.money+=2.5*bet                   #player earns a profit of 15% on bet
            print(p1.money)
            break
        #if the total is 21 but ace card absent player wins as 21!
        elif fidvalue(p1.cards) == 21:
            print("you wom")
            p1.bust=False
            dealr_won=False

    #if the sum of values on dealers cards is less then seventeen and the player has chose to stand the dealer gets one more card
    if not dealr_won:
        while True:
              if fidvalue(d1.cards)<17:
                    print("the dealer hits")
                    d1.cards +=d1.picks(2)              #here we picked two cards because,one card initially is face down so,we firt print the card and add one new card as dealer has to hit.
                    if fidvalue(d1.cards) > 21:
                        d1.bust= True
                        break
                    elif fidvalue(d1.cards)== 21:
                        print("dealer won, blackjack!!!!!!!")
                        d1.bust=False
                        break
                    if fidvalue(d1.cards) >fidvalue(p1.cards):
                        dealr_won =True
                        break
              else:
                  d1.cards += d1.picks(1)                #here as the sum was more than 17 dealer cannot hit and hence we just make the face down card face up
                  print("the dealer stands")
                  if fidvalue(d1.cards) > 21:
                      d1.bust = True
                      break
                  elif fidvalue(d1.cards) == 21:
                      print("dealer won, blackjack!!!!!!!")
                      d1.bust = False
                      break
                  if fidvalue(d1.cards) > fidvalue(p1.cards):
                      dealr_won = True
                      break
              break
    print("\n" *5)                             #for gap between two plays
    print_board(p1.cards, d1.cards)


    if dealr_won:
        print("_"*150)
        print("you lost the game and the money.")
        if p1.bust:
            print("Your cards value more than 21.")
            print("_"*150)
        else:
            print("Better luck next time!")
            print("_"*150)
    else:
        #player gets double the amount of bet as soon as he wins
        p1.money+=2*bet
        print("_"*170)
        print("You won! double the bet credited to your account.")
        if d1.bust:
            print("Dealer's total card value exceeded 21. You got lucky, well played!")
            print("_"*170)

    print("Your account balance is: {}".format(p1.money))
    while True:

                #player choses whether to play again
        play_again=input("Desired to play again? [y/n]: ")
        if play_again=='n':
            break
        #if the player runs short of money he cannot play further
        elif p1.money==0:
            print("insufficient balance")
            break
