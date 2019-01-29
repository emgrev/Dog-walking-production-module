# Dog-walking-production-module
A basic python ACT-R production model. A re-make of a ham and cheese sandwich production module. 

################# walk a dog production model ##################

### Emily Greve ###

# this is a simple ACT-R model
# it uses two production systems and one buffer
# the buffer represents the focus of thought, and is called the focus buffer
# the productions will fire if the production matches the focus buffer
# once a production fires the focus buffer will change

import ccm
log=ccm.log(html=True)

from ccm.lib.actr import *

# python ACT-R requires an environment

class House(ccm.Model):                                # items in the environment look and act like objects
    dog=ccm.Model(isa='dog',colour='black',name='nakoda',location='in_house')
    collar=ccm.Model(isa='collar',location='on_hook')
    leash=ccm.Model(isa='leash',location='on_hook')
    door=ccm.Model(isa='door',location='closed')
    walk=ccm.Model(isa='action',location='not_started')

# create a motor module to do the action
class MotorModule(ccm.Model):
    def call_dog(self):                                # note that technically the motor module is outside the agent
        yield 5                                        # but it is controlled from within
        print "found the dog"
        self.parent.parent.dog.location='with_me'      #self=Motor, parent=MyAgent, parent of parent=House
    def do_collar(self):
        yield 2
        print "the collar is on"
        self.parent.parent.collar.location='on_dog'
    def do_leash(self):
        yield 2
        print "the leash is attatched to the collar"
        self.parent.parent.leash.location='on_collar'
    def do_door(self):
        yield 2
        print "turn the handle and open the door"
        self.parent.parent.door.location='open'
    def do_walk(self):
        yield 10
        print "start walking"
        self.parent.parent.walk.location='started'
        
# now we create the agent

class MyAgent(ACTR):
    focus=Buffer()
    motor=MotorModule()
    
    def init():
        focus.set('find dog')

    def find_dog(focus='find dog'):                                    # if focus buffer has this chunk then...
        print ("I have called the dogs name, she is in the room now")  # print
        focus.set('put on collar')                                     # change the focus buffer
        motor.call_dog()

    def add_collar(focus='put on collar', dog='location:with_me'):     # the rest of the productions are the same
        print ("I caught the dog and now I am putting on her collar")  # but carry out different actions
        focus.set('clip on leash')
        motor.do_collar()

    def add_leash(focus='clip on leash', collar='location:on_dog'):
        print ("I am clipping the leash onto the collar")
        focus.set('open door')
        motor.do_leash()

    def open_door(focus='open door',leash='location:on_collar'):
        print ("I'm holding the leash so the dog doesn't run out")
        focus.set('start walk')
        motor.do_door()

    def start_walk(focus='start walk',door='location:open'):        # wait for the action to complete before stopping
        print ("I have left the house")
        focus.set('stop')
        motor.do_walk()

    def stop_production(focus='stop',walk='location:started'):
        self.stop()                      # stop the agent

Chia=MyAgent()                           # name the agent
env=House()                              # name the environment
env.agent=Chia                           # put the agent in the environment
ccm.log_everything(env)                  # print out what happens in the environment

env.run()                                # run the environement
ccm.finished()                           # stop the environement 




      
