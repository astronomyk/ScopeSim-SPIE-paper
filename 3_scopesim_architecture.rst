- ScopeSim Engine
    - Simulates telescope optical systems in a physically motivated way
    - All telescope systems contain following elements
        - Atmosphere / observatory
        - Telescope optics
        - Relay optics
        - Instrument optics
        - Detector electronics
    - Each optical element has a certain effect on the incoming light that can be described with a combination of 3 dimensions (x, y, lambda)
        - lam       (spectral)
        - x + y     (spatial)
        - lam + y, lam + y, lam + x + y   (spectrospatial)
        - none of lam, x, y (electronic)
    - majority of the effects are not time sensitive, can therefore be described mathematically / analytically
    - hence a telescope system is nothing more than a collection of effects to be applied in the correct order
    - ScopeSim is simply a management system for the abstraction layer around the Effect
    - ScopeSim simulations essentially loop through and apply each effect described in the instrument package
    - Effects objects
        - A standard library is provided by ScopeSim
        - Interface is standardised so custom Effects plugins are possible
        - Two standard functions:
            - apply_to
            - fov_grid
        - data used can be provided in 3 formats
            - ASCII table
            - FITS HDUList (Image, Cube, BinTable)
            - arrays in the YAML description
    - ScopeSim uses Monochromatic field of view slices to apply x+y+lam interdependent effects
        - Based on the telescope parameters a list of quasi monochromatic FOV objects are prepared
            - The FOV objects map a monochromatic section of sky onto the the detector image plane
            - They contain a double WCS to facilite this mapping
        - Iteratively, each FOV object extracts a monochromatic image from the Source provided
        - All relevant effects are are applied to the FOV object
        - The final monochromatic flux image including all artifacts is projected onto a image plane
        - The DetectorArray cuts out relevant sections from the image plane for each Detector FOV
        - Exposure time, readout effects, and other electronic effects are applied
        - The final detector readout images are collected and saved to disk as a FITS file
    - flexibility in level of complexity

