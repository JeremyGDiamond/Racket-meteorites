# Racket-meteorites

The third project of my class on the features and structure of languages. We were instructed to use the language racket (a dialect of lisp) to display a map of the world, adding lines of latitude and longitude, and a dot for every meteorite in a csv file. The dots are then supposed to be sized proportional to the mass of the meteorites on a logarithmic scale. This functionality was to be added to the example program provided. Racket is the professor's favorite language.

You can see the assignment as “CSCI-3415-Program-3b-Fall-2018.pdf”, the sample code as “simple-meteorites-ref.rkt”, my code as “diamond-meteorites.rkt”, and my final report as “report.docx”. The following is adapted from my final report.
### Note
There were 2 proposed project options. I’m only dealing with the one I did here, for obvious reasons.
## Running the Example Code
Our first task was to simply get the example code to run as essentially nobody in the class had ever used racket or lisp. After installing racket and downloading the relevant files I began running the code as is. It yielded the following image. This seems to be identical to the intended state of the code. 
![alt text](https://github.com/JeremyGDiamond/Racket-meteorites/blob/master/screenshots/prechange.PNG "Intended state of sample code")
## Drawing the Lines of Latitude and Longitude
I began from by attempting to draw the latitude and longitude lines. Modeling this off of the point drawing code provided, and making use of the racket drawing documentation, I came up with the following code in main. 

```;; Draw lines
    (for ((lat (in-range -90.0 90.0 10.0)))
      (define-values (x1 y1) (lat-long->x-y canvas lat -180))
      (define-values (x2 y2) (lat-long->x-y canvas lat 180))
      (send canvas-dc set-pen "gray" 1 'solid)
      (send canvas-dc set-alpha 0.5)            
      (send canvas-dc draw-line x1 y1 x2 y2)
    (yield))
    
    (for ((long (in-range -180.0 180.0 10.0)))
      (define-values (x1 y1) (lat-long->x-y canvas -90 long))
      (define-values (x2 y2) (lat-long->x-y canvas 90 long))
      (send canvas-dc set-pen "gray" 1 'solid)
      (send canvas-dc set-alpha 0.5)            
      (send canvas-dc draw-line x1 y1 x2 y2)
    (yield))

    (define-values (x1 y1) (lat-long->x-y canvas -90 0))
    (define-values (x2 y2) (lat-long->x-y canvas 90 0))
    (send canvas-dc set-pen "gray" 3 'solid)
    (send canvas-dc set-alpha 0.5)            
    (send canvas-dc draw-line x1 y1 x2 y2)
    (yield)

    (define-values (x3 y3) (lat-long->x-y canvas 0 -180))
    (define-values (x4 y4) (lat-long->x-y canvas 0 180))
    (send canvas-dc set-pen "gray" 3 'solid)
    (send canvas-dc set-alpha 0.5)            
    (send canvas-dc draw-line x3 y3 x4 y4)
    (yield) 
  ```

The first two blocks draw lines of latitude and longitude respectively, every 10 degrees, using for loops. The second two draw the equator and prime meridian respectively, at a size of 3 rather than 1. This was inserted before the dot drawing code so the dots would be drawn on top of them. That code (removing the meteorite parsing for testing needs) generated the following image.
![alt text](https://github.com/JeremyGDiamond/Racket-meteorites/blob/master/screenshots/drawLines.PNG "Lines of latitude and longitude drawn")
## Logarithmic Scale Resizing of the Drawn Dots
To alter the size of the drawn object on a log scale I defined the following function. It first checks if the string “mass” can be converted into a number. If it passes then the log of that number is evaluated and if not it defaults to 3. If the number is 0 or the log of the number is less than 3 the function returns a 3. Otherwise it returns the log of the mass. Note all of the default 3’s were returned with the statement (+ 1 2). I couldn't figure out a better way to do this due to inexperience with lisp.
```
(define (mass->size mass)

  (if (string->number mass)
      (cond
        [(= (string->number mass) 0) (+ 1 2)]
        [(< (log (string->number mass)) 3) (+ 1 2)]
        [else (log (string->number mass))]
       )
   (+ 1 2)    
  )
)
```
Replacing the default size setting with a call to this function generated the following image.
![alt text](https://github.com/JeremyGDiamond/Racket-meteorites/blob/master/screenshots/finalProduct.PNG "Final product with dots logarithmically scaled to mass")
