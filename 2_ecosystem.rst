ScopeSim Ecosystem
==================

Outline
-------
- Overview
    - What are the three pieces
        - ScopeSim engine
        - ScopeSim templates
        - Instrument reference database
    - How the 3 pieces fit together
        - Diagram of this
    - Why split them up
        - External parties can be responsible for maintaining their own packages
        - Astronomers will have very case specific descriptions of on-sky objects
        - Different use cases require different levels of complexity for both input and output data
        - Yet, telescopes and instruments have a series of common elements which can be encapsulated into a single framework





Lego analogy
------------
The Effect object is the Lego brick
The IRDB is the toy shop, each instrument package is a box of Lego
The YAML config files are the instruction manuals
ScopeSim engine is the child
The FITS output image is the built model
The templates are the true object that the lego model is recreating
