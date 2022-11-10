# THE ENGIMA MACHINE SOURCE CODE

# Global Variables

# Do NOT change anything below, these are static.
# rotor one and rotor two both have notches on number 8 
rotor_one_notch = 1
rotor_two_notch = 1

reflector_board = ["l","j","q","k","v","h","m","f","s","b","d","a","g","o","n","z","c","y","i","u","t","e","x","w","r","p"]

wheel_one = ["h","e","z","y","v","u","i","q","t","a","x","m","g","s","r","f","w","b","d","k","l","c","j","o","n","p"]
wheel_two = ["m","s","e","l","c","w","f","g","q","y","h","n","b","o","v","k","t","r","j","a","d","i","p","x","u","z"]
wheel_three = ["l","n","x","d","f","b","j","a","r","t","z","i","v","c","e","w","o","k","p","h","y","u","q","g","s","m"]
wheel_four = ["d","h","l","z","p","s","m","e","r","k","o","u","g","x","b","n","c","v","f","q","a","w","j","y","t","i"]
wheel_five = ["e","t","o","i","b","f","k","q","m","l","v","w","h","j","d","s","p","z","c","n","x","a","g","u","y","r"]

# Choose any of the five wheels above 
rotor_one_slot = wheel_one
rotor_two_slot = wheel_two
rotor_three_slot = wheel_three

# Choose numbers between 1-26 
rotor_one_dial = 4
rotor_two_dial = 6
rotor_three_dial = 7

# Choose to create different pairs of the letters (must be unique pairs) 
plug_board = ["oj","wt","mr","ep","kz","qi","sd","ng","hy","bf"]


# function to emulate the reflector
def reflector(reflector_input):
    
    global reflector_board

    # return the letter that's connected to the input letter
    letter_index = ord(reflector_input) - 97
    return reflector_board[letter_index]

# function to emulate the third rotor 
def rotor_three(rotor_three_input, direction):

    global rotor_three_slot
    global rotor_two_notch

    # return letter in input's index (place)
    if direction == "F":
        rotor_index = (ord(rotor_three_input) - 97) % 26
        return rotor_three_slot[rotor_index]
    
    # find input letter and return letter associated with index
    if direction == "B":
        
        letter = chr(rotor_three_slot.index(rotor_three_input) + 97) 

        if rotor_two_notch == 8:
            rotor_three_slot.append(rotor_three_slot.pop(0))

        if rotor_two_notch > 26:
            rotor_two_notch = 0 
   
        return letter


# function to emulate the second rotor 
def rotor_two(rotor_two_input, direction):

    global rotor_two_slot
    global rotor_one_notch
    global rotor_two_notch

    # return letter in input's index (place)
    if direction == "F":
        rotor_index = (ord(rotor_two_input) - 97) % 26
        return rotor_two_slot[rotor_index]
    
    # find input letter and return letter associated with index
    if direction == "B":

        letter = chr(rotor_two_slot.index(rotor_two_input) + 97)
             
        if rotor_one_notch == 8:
            rotor_two_slot.append(rotor_two_slot.pop(0))
            rotor_two_notch += 1

        if rotor_one_notch > 26:
            rotor_one_notch = 0 
            
        return letter


# function to emlulate the first rotor
def rotor_one(rotor_one_input, direction):

    global rotor_counter
    global rotor_one_slot

    # return letter in input's index (place) 
    if direction == "F":
        rotor_index = (ord(rotor_one_input) - 97) % 26
        return rotor_one_slot[rotor_index]

    # find input letter and return letter associated with index
    if direction == "B":
        letter = chr(rotor_one_slot.index(rotor_one_input) + 97) 
        rotor_one_slot.append(rotor_one_slot.pop(0))

        return letter


# function to emulate plug board       
def plug(plug_input):

    global plug_board

    # take letter, find on plugboard, return the letter paired with it
    # this will be sent to the static rotor, but since nothing changes we don't have a function for the static rotor. 
    for pair in plug_board:
        if plug_input in pair:
            for letter in pair:
                if letter != plug_input:
                    return letter
                
    return plug_input

# set the combination of the rotors 
def combo(one = 1, two = 1, three = 1):
    
    global rotor_one_slot
    global rotor_two_slot
    global rotor_three_slot

    global rotor_one_notch
    global rotor_two_notch

    one_combo = one - 1
    two_combo = two - 1
    three_combo = three - 1

    for i in range(one_combo):
        rotor_one_slot.append(rotor_one_slot.pop(0))
        rotor_one_notch += 1

    for i in range(two_combo):
        rotor_two_slot.append(rotor_two_slot.pop(0))
        rotor_two_notch += 1

    for i in range(three_combo):
        rotor_three_slot.append(rotor_three_slot.pop(0))
        

# function to encript and decrypt the message
def encode():

    global rotor_one_notch
    
    global rotor_one_dial 
    global rotor_two_dial 
    global rotor_three_dial 

    print("Enigma Machine")
    print("")
    
    plaintext = input("Type in what you want to encode/decode: ")
    plaintext = plaintext.lower()
    

    combo(rotor_one_dial, rotor_two_dial, rotor_three_dial)
    
    ciphertext = ""

    # for each letter typed, it lights up a letter on the board
    # for the code, we just add it to the ciphertext
    for i in plaintext:

        # if a letter
        if 97 <= ord(i) <= 122: 
        
            plug_letter = plug(i) 
            
            rotor_one_letter_I = rotor_one(plug_letter, "F")
            rotor_two_letter_I = rotor_two(rotor_one_letter_I, "F") 
            rotor_three_letter_I = rotor_three(rotor_two_letter_I, "F") 
            
            reflector_index = reflector(rotor_three_letter_I) 
            
            rotor_three_letter_II = rotor_three(reflector_index, "B") 
            rotor_two_letter_II = rotor_two(rotor_three_letter_II, "B")
            rotor_one_letter_II = rotor_one(rotor_two_letter_II, "B")
            plug_letter_II = plug(rotor_one_letter_II)

            ciphertext += plug_letter_II

            rotor_one_notch += 1

        # if a space, number, or symbol 
        else:
            ciphertext += i

    print(ciphertext)

encode()








