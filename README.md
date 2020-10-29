# Crowdedness prediction in public transport under Covid-19

Project proposal for DTU course 02456 Deep learning (2020)

## Data sets

### AFC - Journey Matched (data-od-*xxx*.csv.gz)
Each row corresponds to one passenger trip on a scheduled vehicle journey (i.e. every tap in)
- ``JourneyRef`` A unique reference to the planned journey, that this passenger trip (tap in) was matched to using vehicle journey assignment.
- ``Time`` The time for the passenger trip in local time, specifically the departure from ``FromStopPointID`` as recorded by AVL, or the planned departure if no AVL data was recorded for that journey point.
- ``LineNumber`` The line the vehicle journey is scheduled on, e.g. ``300S`` = 300.
- ``LineDirectionCode`` The direction of the line the vehicle journey is scheduled on. e.g. ``1`` or ``2``. There is really no logic in the choice of which of the direction is ``1``, respectfully ``2``.
- ``FromStopPointID`` Where the passenger boarded (tapped in) on the vehicle journey.
- ``ToStopPointID`` Where the passenger alighted from the vehicle journey. This can be observed directly (if the passenger tapped out), or indirectly, if the passenger alighed (without tapping out), and transfered to another public transport service. In the latter, we will assign the stop point closest (in distance) to the passengers next tap in.
- ``FromTimingPointAbbreviation`` The upstream closests timing point from where the passenger boarded (tapped in) on the vehicle journey. If the passenger tapped on at a timing point, this will be assigned.
- ``ToTimingPointAbbreviation`` The downstream closests timing point from where the passenger alighted (tapped out or transfered) on the vehicle journey. If the passenger tapped on at a timing point, this will be assigned.
- ``FromTimingPointOrder`` A topological order of the ToTimingPoint on Line / Line Direction level. Can be used to build "topological correct" OD-matrices.
- ``ToTimingPointOrder`` A topological order of the FromTimingPoint on Line / Line Direction level. Can be used to build "topological correct" OD-matrices.

### AFC - Timing Point OD Matrices (od-demand-*xxx*.npy.gz)
Are contrusted from the above dataset for a single line at a time. To avoid very sparse OD-matrices we have used the Timing Point assignments. This reduces the OD-sizes with an order of magnitude. Consists of a numpy array of size ``T x W x W``, where `T` is number of timestamps, and `W` is the number of timing points on the given line.

The values of ``T`` is available in a seperate file od-*xxx*-time.npy.gz, and the values of `W` (i.e. the timing point abbreviations) in od-*xxx*-stops.npy.gz

The value od the OD-matrix at (*i*,*j*,*k*) is the number of passengers traveling from timing point *j* to *k* at time *i*.

### Supply - Time table (data-journey-*xxx*.csv.gz)
