# Dog-walking-production-module
A basic python ACT-R production model. A re-make of a ham and cheese sandwich production module. 

################ walk a dog production model ##################

### Emily Greve ###

# this is a simple ACT-R model
# it uses only one production system and one buffer
# the buffer represents the focus of thought, and is called the focus buffer
# the productions will fire if the production matches the focus buffer
# once a production fires the focus buffer will change

import ccm
log=ccm.log()

from ccm.lib.actr import *

# python ACT-R requires an environment
# in this case we will not be putting anything in the environment

class MyEnvironment(ccm.Model):
    pass                       # we 'pass' on putting anything in the environment

# now we create the agent

class MyAgent(ACTR):
    focus=Buffer()

    def init():
        focus.set('find dog')

    def find_dog(focus='find dog'):                                    # if focus buffer has this chunk then...
        print ("I have called the dogs name, she is in the room now")  # print
        focus.set('put on collar')                                     # change the focus buffer

    def add_collar(focus='put on collar'):                             # the rest of the productions are the same
        print ("I caught the dog and now I am putting on her collar")  # but carry out different actions
        focus.set('clip on leash')

    def add_leash(focus='clip on leash'):
        print ("I am clipping the leash onto the collar")
        focus.set('open door')

    def open_door(focus='open door'):
        print ("I'm opening the door, still holding the leash so the dog doesn't run out")
        focus.set('start walk')

    def start_walk(focus='start walk'):
        print ("I have left the house and am starting the walk")
        focus.set('stop')

    def stop_production(focus='stop'):
        self.stop()                      # stop the agent

Chia=MyAgent()                           # name the agent
ChiaHouse=MyEnvironment()                # name the environment
ChiaHouse.agent=Chia                     # put the agent in the environment
ccm.log_everything(ChiaHouse)            # print out what happens in the environment

ChiaHouse.run()                          # run the environement
ccm.finished()                           # stop the environement 



      
