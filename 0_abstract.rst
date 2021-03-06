Possible conferences preferences
================================
1. Ground-based and Airborne Instrumentation for Astronomy VIII
https://spie.org/AS/conferencedetails/astronomy-ground-based-instrumentation

2. Modeling, Systems Engineering, and Project Management for Astronomy IX
https://spie.org/AS/conferencedetails/astronomy-modeling-systems-engineering


Abstract for online - 1000 characters
=====================================

ScopeSim is a python-based software framework that allows the user to generate
simulated images of astronomical objects as would be seen by the detectors of
an arbitrary optical system. The package allows multiple types of optical system
to be modelled: from simple imaging cameras to integral field unit
spectrographs.

The software architecture is based on a physically motivated model common to all
astronomical optical systems. The model mimics the main components of any real
optical system: a spectro-spatial distribution of light passing through a series
of optical sub-systems (atmosphere, telescope, fore-optics, instrument) before
being converted into photo-electrons in a detector.

ScopeSim itself is however instrument-agnostic. When in use, a ScopeSim
instrument model loads data from ``instrument packages'', which are
downloaded as needed from an external server. Currently such packages have been
created for two of the ELT's first-light instruments: MICADO and METIS.


Abstract for technical review - 20000 characters
================================================

What is ScopeSim?
-----------------
ScopeSim is a pythonic instrument data simulation framework, which allows the
user to generate output data from an astronomical optical (telescope +
instrument) system in a variety of formats. The package has been designed and
written in such a way that is is capable of modelling any system that follows
the standard astronomical optical train: a spectro-spatial distribution of light
passing through a series of optical sub-systems (atmosphere, telescope,
fore-optics, instrument) before being converted into photo-electrons in a
detector.

The software itself is agnostic to the optical train it must model, and
relies on the information contained inside the instrument packages.
These contain the descriptions of any optical aberrations or effects incurred
along the optical train, the data and/or values associated with these
effects, and, in special cases, also the software implementation of any
custom effects. The online ScopeSim documentation provides a detailed
description of the data formats and interfaces that need to be observed when
constructing an instrument package.

The raison d'etre for ScopeSim is to fill the niche that exists between the
two major families of observation simulations currently pursued by instrument
consortia: the exposure time calculator (ETC) and the end-to-end (E2E) model.
ETCs provide the most basic information about the observability of a target,
generally be referencing pre-calculated look-up tables, and are therefore
well suited as general-purpose support tools for those interested in the
capabilities of an instrument. E2E models are at the other extreme, and
often recompute the paths of photons or light-rays through a detailed model
of an optical system every time a simulation is run. The very high level of
accuracy achieved by E2E simulations comes at the expense of computational
efficiency. Such simulations are indispensable for understanding the fine
details of the true optical characteristics of an instrument. However they are
impractical for rapid turn-around applications such as refining an
observing strategy or determining the extent to which a bright field star
will impact the outcome of a observation.

Given the ever increasing pressure for telescope time as well as the
increasing complexity of instrumentation systems, there is a very real need
for a set of observation simulation tools which allow astronomers to go
beyond the single-value metric of an ETC, but which do not require the
infrastructure and time-investment associated with E2E simulations. In recent
years several consortia have developed solutions to fill this niche, driven
primarily by the needs of the in-house science team and the astronomical
community. A prime example is the software suite developed by the JWST team.
Unfortunately though there is still no generic software
package which allows astronomers to compare observations with different
instruments and telescopes on a single coherent framework. It is exactly this
niche which ScopeSim aims to fill.


Scope of simulations
--------------------
ScopeSim can generate simulated instrument data in a variety of formats and
with varying levels of fidelity, depending on the use case. The original purpose
of ScopeSim was to provide mock data sets to the MICADO data reduction pipeline
team for testing purposes. These data sets include a series of raw images as
would be produced by the detector electronics at read-out time. As such they
need to include all the optical artifacts produced by the entire optical system.

Such data sets are however large and ungainly, and are not suitable for the
every-day astronomer. Simulating a full raw data set and then reducing this
down to a science-ready data product for a science case feasibility study is
a taxing task, both computationally and mentally. Hence different instrument
package configurations can be used to circumvent these time consuming steps.
Instead, ScopeSim can generate a single output data set (either images or
spectra) to which reduction artifact residuals are applied. These residuals
represent the average combined level of data degradation that can be expected to
occur during the observation and reduction steps. Such data sets are therefore
comparable to the final science-ready data that can be expected for a given
instrument.

Depending on the complexity of a given instrument, such simulations may also
take longer than is necessary to answer a given question. For example, the user
will not want to invest minutes of computation time to determine whether a
single star is visible for a given magnitude and exposure time. ScopeSim can
also provide simulated data products similar in scope to the output of an
exposure time calculator (ETC) by using an ETC-style instrument package.
Such instrument packages contain relatively little information about the
fine details of optical artifacts (generally a single system transmission curve
and a description of the PSF) however return the requested signal to noise
information in a very timely fashion.

It should be stressed once more that ScopeSim relies on artifact descriptions
generated by other teams to create the simulated data sets. Hence, in contrast
to E2E models, the quality of the output data is only ever as good as the
quality of the data contained in the instrument packages. However assuming a
high level of detail is included in a package, ScopeSim can provide
simulated data sets that cover a large range of applications.


How does ScopeSim work?
-----------------------
ScopeSim consists of two major software components and a database system. The
software components are split between the tasks of describing an on-sky object
and observing this object. The database contains the data necessary to model the
effects of the optical system on the incoming light. These three components are
referred to as the ScopeSim engine (scopesim), the ScopeSim templates library
(scopesim_templates) and the instrument reference database (IRDB). Each
component will be described in detail below.


The ScopeSim engine
+++++++++++++++++++
The main workhorse class of the ScopeSim engine is the OpticalTrain class. This
class is responsible for accepting a Source object from the templates library,
extracting the relevant field of view, generating a model of the optical system
in memory from data supplied by the IRDB, applying all optical and electronic
aberrations and effects, and finally generating the output in the format
defined by the user (FITS images, spectra, etc).

An OpticalTrain uses a physically motivated series of actions to observe an
on-sky object. ScopeSim uses neither ray-tracing nor Fraunhofen/Frenel-optics
methods to create images of the target. Instead it cuts the target description
into many spatial and spectral slices, applies the necessary effects to each
slice, and projects each slice onto a focal plane canvas, known as the
``photo-electron expectation map''. Here electronic effects are applied and the
Detector objects extract the information needed to produce the final data
products. Effects are described by Python classes and can contain
either analytical or numerical descriptions of the action. While the ScopeSim
engine package already contains a large list of common optical effects, custom
effects can be added by third parties using the IRDB effect plug-in framework.

Internally the OpticalTrain contains two manager classes: the field-of-view
manager (FOVManager) and the effects manager (OpticsManager). The FOVManager
maintains an overview of which sections of the spectro-spatial domain have been
sampled and projected onto the focal plane. The OpticsManager collates and
controls when and how each optical effect is applied to the incoming flux
slices. For example, it would be computationally inefficient to apply the
transmission curves of each optical surface individually to each flux slice, so
the OpticsManager generates a system transmission curve when the OpticalTrain is
initialised and applies only this single curve to the target's associated
spectra.

This architecture gives ScopeSim great flexibility. By splitting the Source
object into quasi-monochromatic image chunks, ScopeSim mimics what happens
inside a real optical system. Each chunk is shifted, distorted, blurred, and
extincted as if it were passing through the various optical surfaces
of a real observatory. Due to this method, most variations on the classic
optical system can be implemented in ScopeSim with relative ease. A long slit
spectrograph is an imager where the monochromatic slices are dispersed
over the focal plane. An integral field spectrograph can be modelled as a series
of long slit spectrographs. A fibre-fed multi-object spectrograph is a series of
hexagonal ``slits'' which throw away spatial information. A coronagraph is an
imager with a customised PSF description. Each of these systems can be
implemented relatively painlessly with the ScopeSim architecture as long as an
analytical or (interpolatable) numerical description exists for each
instrumental effect in the form of a ScopeSim Effect object.

Not only does this software architecture allow most types of instruments to be
implemented in ScopeSim, it also means that astronomers will be able to use a
single platform to compare simulated observations in an apples-to-apples
fashion.

Effects already implemented in the ScopeSim engine include:
- Spectral effects:
    - Mirror transmission
    - Mirror greybody emission
    - Customisable sky transmission and emission, downloaded directly from the
      ESO SkyCalc tool API

- Spatial effects:
    - Seeing and diffraction limited PSF, both varying and constant in the
      spatial and spectral domains
    - Spectrograph trace maps
    - Atmospheric diffraction
    - Non common path aberrations
    - Wind shake and vibrations
    - Optics distortions

- Electronic effects
    - Quantum efficiency
    - Dark current
    - Various form of read noise (mainly for HgCdTe detectors)
    - Linearity and saturation
    - Bad pixels
    - Shot noise

This is not an exhaustive list of effects, and more will be added over time as
the package matures. The community is also invited to contribute to this list
via Pull-Requests on the ScopeSim GitHub page.


The ScopeSim template library
+++++++++++++++++++++++++++++
The ScopeSim template library is a collection of helper functions which produce
Source objects to be observed by the ScopeSim engine. A Source object must
contain a spatial and a spectral description of the target object. The spatial
description can be in the form of a table of positions if the target objects are
point sources, or as a flux map (e.g. FITS image, bitmap, etc.) if the
target is an extended object like a galaxy. The spectral description can either
be a data array containing wavelength and flux information, or a simple
description of a spectral source (e.g. ``A0V'') scaled to a certain magnitude
(V=15).

The associated spectrum, much like any input image can have any resolution,
however it should be clear to the user that ScopeSim output data will never
have better resolution than the original input data. If the user does not
possess the spectra required to build a Source object, they can choose
one of the in-built spectra from either the Pickles (1998) stellar spectra
catalogue or the Brown (2014) galaxy spectra catalogue.

Given that the user will spend the majority of their time building a description
of their target, and that the associated functions have nothing to do with the
core code of the ScopeSim engine, we felt it prudent to maintain the
ScopeSim_templates code in a separate package. This also allows code to be added
to and removed from the repository without affecting the ScopeSim engine
package. Hence the template library can follow a completely independent release
schedule.


Instrument packages and the IRDB
++++++++++++++++++++++++++++++++
In order to maintain as much flexibility as possible, the ScopeSim engine
code base must remain completely detached from any instrument specific data.
Not only does this allow for a clean interface between the software design and
the data being delivered by an instrument consortium, it allows the
functionality of the software to written in a independently testable manner.
It is also just good software practice.

Any instrument specific data (e.g. transmission values, PSF
descriptions, mode specific values, etc.) are kept in a zip archive and
formatted according to a set of interface guidelines. Files belonging to a
package are kept under version control on Github. Whenever a new package release
is requested, these files are tested, bundled, and uploaded onto a server.

ScopeSim downloads packages directly from this server whenever the user
instructs ScopeSim to use a certain instrument for a simulation run. If the
package has been used for a prior simulation, it will be loaded from the local
astropy cache folder, unless a newer version is available. In this case the
newer version is downloaded and replaces the cached version. This caching system
allows ScopeSim to also work when the host computer is offline.

Aside from enabling a strict demarcation between code and data, an
instrument package can also serve other projects within an instrument
consortium. The packages serve as a living-repository for the most up-to-date
technical descriptions of certain elements of the instrument. For example, the
package will contain and the very minimum a list of all filters and their
transmission curves and a description of the expected PSF.

The IRDB already contains packages for the following instruments: MICADO and
METIS at the ELT, and HAWKI at the VLT. More will be added in the near future.
The guidelines for creating custom packages are in the ScopeSim online
documentation and the community is encouraged to contribute to the library of
packages already available.


Conclusion
++++++++++
ScopeSim is an attempt to create a generalised instrument data simulation
framework which can mimic the data output of a large majority of the instruments
attached to current and future telescopes. Such a package will be of benefit to
both the astronomers and pipeline developers. When writing observing
time proposals, astronomers will be able to provide visualisations of the
expected outcome of their observation run. This in turn will lend credibility
to the proposals, as well as provide a visual success criterion for the
telescope operator. Furthermore the level to which optical effects (such as PSF
diffraction spikes) will impact the outcome of a science case can be studied
before an observing proposal is written, thus further saving time and effort.



Outline
-------
What is ScopeSim

Scope of simulations
- Full raw data products for pipelines
- Reduced stacked data for science feasibility studies
- Restricted mode advanced ETC functionality

How does ScopeSim work?
- brief description
- 4 planes
- 3 control classes
- effects objects
- simulation run

Where does ScopeSim get its data from?
- IRDB
- MICADO, METIS, HAWKI
- Other packages
