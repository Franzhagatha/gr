# gr
Two collind neutron stars

from vpython import *


## Create graphs for THINGS (later).
#tempscene = gdisplay(title='temperature',x=0,y=0) 
#tempgraph = gcurve(color=color.red)
#presscene = gdisplay(title='pressure',x=0,y=400) 
#presgraph = gcurve(color=color.blue)



mass_list = [0] # Start mass list.

mass_list.append(0.8) # Star 1
mass_list.append(0.8) # Star 2

nbbs = len(mass_list)-1

ibb = 1

bb_list = [0] # Start body list.
vel_list = [0] # Start velocity list.

# Set up body ibb = 1.
x = 0
y = 0
z = 0
rad = mass_list[1]*.1
col = color.white
bb_list.append(sphere(radius = rad, # Multiplication by 10 to make more visible.
                      pos =vector(x,y,z),
                      color=col,
                      make_trail = True))
vx = 0
vy = -.63
vz = 0
vel_list.append(vector(vx,vy,vz))
    
# Set up body ibb = 2.
x = 1
y = 0
z = 0
rad = mass_list[2]*.1
bb_list.append(sphere(radius = rad, # Multiplication by 10 to make *slightly* more visible.
                      pos =vector(x,y,z),
                      color=col,
                      make_trail = True))
vx = 0
vy = .63
vz = 0
vel_list.append(vector(vx,vy,vz))

force_list = [0]
ibb = 1
while ibb <= nbbs:
    force_list.append(vector(0,0,0))
    ibb = ibb + 1

t = 0
dt = 0.01
G = 1.0 # Graviational constant.

# Calculate total energy.
kinetic = 0.0
ibb = 1
while ibb <= nbbs:
    kinetic = kinetic + 0.5*mass_list[ibb]*mag(vel_list[ibb])**2
    ibb = ibb + 1

potential = 0.0
ibb = 1
while ibb <= nbbs-1:
    ibb2 = ibb+1
    while ibb2 <= nbbs:
        potential = potential - G*mass_list[ibb]*mass_list[ibb2]/mag(bb_list[ibb].pos-bb_list[ibb2].pos)
        ibb2 = ibb2 + 1
    ibb = ibb + 1

# Calculate total angular momentum.
angmom = vector(0,0,0)
ibb = 1
while ibb <= nbbs:
    angmom = angmom + cross(bb_list[ibb].pos,mass_list[ibb]*vel_list[ibb])
    ibb = ibb + 1

print ('total energy = '),kinetic + potential
print ('total angmom = '),angmom

while True: # Begin motion loop.

    rate(100)

    ibb = 1
    while ibb <= nbbs:
        force_list[ibb] = vector(0,0,0)
        ibb = ibb + 1

    ibb = 1
    while ibb <=nbbs:
        
        ibb2 = ibb + 1
        while ibb2 <= nbbs:
            r = bb_list[ibb].pos - bb_list[ibb2].pos
            rmag = mag(r)
#            index = ibb*(nbbs-1) + ibb2 # Might need this at some point?
            rhat = r/rmag
            # Calculate force on ibb.
            force_list[ibb] = force_list[ibb] - G*mass_list[ibb]*mass_list[ibb2]/rmag**2 * rhat
            force_list[ibb2] = force_list[ibb2] - force_list[ibb]

            ibb2 = ibb2 + 1
        
        vel_list[ibb] = vel_list[ibb] + force_list[ibb]/mass_list[ibb] * dt # Update body ibb velocity.
        bb_list[ibb].pos = bb_list[ibb].pos + vel_list[ibb]*dt # Update body ibb position.
        ibb = ibb + 1

    t = t + dt

## Will graph THINGS later!
##    tempgraph.plot(pos=(t,temp))
##    presgraph.plot(pos=(t,avgpressure))
