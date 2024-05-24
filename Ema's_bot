from __future__ import print_function
import time
from sr.robot import *

# Thresholds for distance and angle
a_th = 0.5  
d_th = 0.4 
d_th_2_pair = 0.6 

# A set to store paired golden boxes
paired_boxes = set()  

# Initialize the robot
R = Robot()

# Function to drive for a given time
def drive(speed, seconds):
    R.motors[0].m0.power = speed
    R.motors[0].m1.power = speed
    time.sleep(seconds) 
    R.motors[0].m0.power = 0
    R.motors[0].m1.power = 0

# Function to turn for a given time
def turn(speed, seconds):
    R.motors[0].m0.power = speed
    R.motors[0].m1.power = -speed
    time.sleep(seconds)
    R.motors[0].m0.power = 0
    R.motors[0].m1.power = 0

# Function to search for a new token
def Search4NewToken():
    NewToken = None
    dist = 100
    for token in R.see():
        if token.dist < dist and token.info.code not in paired_boxes: #if the token is not paired
            dist = token.dist
            NewToken = token.info.code
           
    if dist == 100:
        return None
    else:
        return NewToken
        
# Function to search for a paired token
def SearchPairedToken():    
    dist = 100
    PairedToken = None  
    for token in R.see():
        if token.dist < dist and token.info.code in paired_boxes: #if the token is paired
            dist = token.dist
            PairedToken = token.info.code
    if dist == 100:
            return None
    else:
            return PairedToken  
    

# Function to search for a token with a given code
def SearchToken(code):
    dist = 100
    for token in R.see():
      if token.info.code == code: #if the token has the given code
        if token.dist < dist: 
            dist = token.dist
            rot_y = token.rot_y  
    if dist == 100:
        return -1, -1
    else:
        return dist, rot_y

# Function to reach a token with a given code
def reach_token1(code,handle):
    print("I'm looking for Token",code)
    var = True
    while var: # we look for the token until we find it
        dist, rot_y= SearchToken(code) # we look for markers
        if dist == -1:
            print("I can't see",code," token!!")
            turn(100, 0.01)  # if no markers are detected, the program ends
        elif dist < d_th_2_pair and handle==True or dist < d_th and handle==False:
            print("Found Token",code) 
            var = False                                         
        elif rot_y < -a_th:  # if the robot is not well aligned with the token, we move it on the left
            print("Left a bit for Token",code)
            turn(-2.5, 0.001)
        elif rot_y > a_th:  # if the robot is not well aligned with the token, we move it on the right
            print("Right a bit for Token",code)
            turn(+2.5, 0.001)
        elif -a_th <= rot_y <= a_th:  # if the robot is well aligned with the token, we go forward
            print("Ah, here we are, for Token",code)
            drive(100, 0.1)

# Function to bring a token with a given UnpairedToken to a given PairedToken
def BringToken_i_2_1(UnpairedToken,PairedToken):
    handle=False
    reach_token1(UnpairedToken,handle) #we reach the unpaired token
    R.grab()
    handle=True
    print("Gotcha!")
    print("I'm going to bring",UnpairedToken,"to",PairedToken)
    reach_token1(PairedToken,handle) #we reach the paired token
    R.release()
    paired_boxes.add(UnpairedToken) #we add the unpaired token to the set of paired tokens
    print("Released!")
    drive(-100, 0.5)

# Main function to bring all tokens to one place near the first token seen
def main():
    time1=0
    FindFirstToken=True
    FirstToken=None

    while FindFirstToken : #we look for the first token
        time1=0
        if FirstToken == None : #if we don't find the first token
            FirstToken = Search4NewToken()
            paired_boxes.add(FirstToken)
            print("FirstToken", FirstToken)
            FindFirstToken=False
            FindUnpairedToken=True

            while FindUnpairedToken: #we look for unpaired tokens
                UnpairedToken=None
                UnpairedToken = Search4NewToken()
                print("UnpairedToken", UnpairedToken)
                if UnpairedToken != None: #if we find an unpaired token
                    time1=0
                    FindUnpairedToken=False
                    FindPairedToken=True

                    while FindPairedToken: #we look for paired tokens
                        PairedToken=None
                        PairedToken = SearchPairedToken()
                        print("PairedToken", PairedToken)
                        if PairedToken != None: #if we find a paired token
                            print("PairedToken ", PairedToken)
                            time1=0
                            BringToken_i_2_1(UnpairedToken,PairedToken)
                            FindPairedToken=False
                            FindUnpairedToken=True
                        elif PairedToken == None: #if we don't find a paired token
                            print("I don't see any paired token!!")
                            turn(100, 0.1)
                            time1=time1+1
                            if time1==20: #if we don't find a paired token for 20 seconds
                                print("all token are not paired click esc to close")
                                FindPairedToken=False

                elif UnpairedToken == None: #if we don't find an unpaired token
                    print("I don't see any token!!")
                    turn(100, 0.1)
                    time1=time1+1
                    if time1==20: #if we don't find an unpaired token for 20 seconds
                        print("all token are paired click esc to close")
                        print("Time taken: ", time.time()-start)
                        exit()

        elif time1<20: #if we don't find the first token for 20 seconds
            print("I don't see any token!!")
            turn(100, 0.1)  
            time1=time1+1
            if time1==20: #if we don't find the first token for 20 seconds
                print("there are no token")
                exit()

start = time.time()
# Call the main function
main()

