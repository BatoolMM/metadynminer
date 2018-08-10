# Metadynminer

Metadynminer is R packages for reading, analysis and visualisation of metadynamics HILLS files produced by Plumed.

```R
# Install from R repository
install.packages("metadynminer") # in future will be added to R repository

# Load library
library(metadynminer) # in future will be added to R repository

# Read hills file
hillsf<-read.hills("HILLS", per=c(TRUE, TRUE)) # done

# Sum two hills files
hillsf+hillsf # done

# Summary of a hills file
summary(hillsf) # done

# Plot CVs
plot(hillsf) # done

# Plot heights
plotheights(hillsf) # done

# Calculate FES by bias sum (alternatively use fes2 for conventional calculation)
afes<-fes(hillsf, tmin=5000, tmax=10000) # done, to do manual

# Sum two FESes
afes+afes # done, to do manual

# Calculate and substract min, max or mean from a FES
afes<-afes-min(afes) # done, to do manual

# Summary of FES
summary(afes) # done, to do manual

# Plot FES
plot(afes) # done

# Find minima
minima<-fesminima(fes) # done, to do manual

# Summary of minima
summary(minima) # done, to do manual

# Create empty minima
minima<-emptyminima(fes) # done, to do manual

# Create ad hoc minimum
minima<-oneminimum(fes, cv1=0, cv2=0) # done, to do manual

# sum minima
fesminima(fes) + oneminimum(fes, cv1=0, cv2=0) # done, to do manual

# Plot free energy minima
plot(minima) # 2D done, 1D to do, to do manual

# Calculate free energy profile for minima
prof<-feprof(minima) # done, to do manual

# Find transition path
# Summary of transition path
```

# Tips and Tricks
## Publication quality figures

Following script can be used to generate a publication quality figure (8x8 cm, 600 dpi):
```R
hillsf <- read.hills("HILLS", per=c(T,T))
tfes<-fes(hillsf)
png("filename.png", height=8, width=8, units='cm', res=600, pointsize=6)
plot(tfes)
dev.off()
```

## Making FES relative to the global minimum
You can set free energy minimum to zero by typing:
```R
tfes <- tfes - min(tfes)
```

## Making movie
Individual snapshots of a movie can be generated by:
```R
hillsf <- read.hills("HILLS", per=c(T,T))
tfes<-fes(hillsf, tmax=100)
png("snap%04d.png")
plot(tfes, zlim=c(-200,0))
for(i in 1:299) {
 tfes<-tfes+fes(hillsf, tmin=100*i+1, tmax=100*(i+1))
 plot(tfes, zlim=c(-200,0))
}
dev.off()
```
These files can be concatenated by a movie making program such as mencoder.

If you instead want to see flooding, type:
```R
hillsf <- read.hills("HILLS", per=c(T,T))
tfes<-fes(hillsf)
png("snap%04d.png")
plot(tfes, zlim=c(-200,0))
for(i in 0:299) {
  tfes<-tfes + -1*fes(hillsf, tmin=100*i+1, tmax=100*(i+1))
  plot(tfes, zlim=c(-200,0))
}
dev.off()
```

## Transforming CVs
If you want to use degrees instead of radians on axes, set axes=F in the plot function and then plot
(without closing the plot window!) both axes separately.
```R
plot(tfes, axes=F)
axis(2, at=-3:3*pi/3, labels=-3:3*60)
axis(1, at=-3:3*pi/3, labels=-3:3*60)
```
The expression -3:3 will generate a vector {-3,-2,-1,0,1,2,3}, which can be multiplied by pi/3
(tick positions in radians) or by 60 (tick positions in degrees). If you want to transform just
one axis, e.g. the horizontal one while keepinng the vertical unchanges, simply type `axis(2)`
for the vertical one.

## Shifting a periodic CV
It may happen that some simulations with a torsion CV it may be difficult to analyse and visualize
it in the range -pi - +pi. However, this problem is not very common so we did not make any user friendly way
how to solve this and it can be solved in a user unfriendly way. It is possible to move the whole free
energy surface and corresponding x or y values within the free energy surface object. For example,
if you want to shift free energy surface to have phi from 0 to 2pi you can do this:
```R
hillsf <- read.hills("HILLS", per=c(T,T))
tfes<-fes(hillsf)
tfes$fes<- rbind(tfes$fes[129:256,], tfes$fes[1:128,])
  # replace 128, 129 and 256 if tfes$rows!=256
tfes2$x<-tfes2$x+pi
plot(tfes2)
```
