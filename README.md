# Crowdedness prediction in public transport under Covid-19

Project proposal for DTU course 02456 Deep learning (2020). You should have recieved a link to data, if not please contact your project supervisor.

## Data sets

### AFC - Journey Matched (data-od-*xxx*.csv.gz)
Each row corresponds to one passenger trip on a scheduled vehicle journey (i.e. every tap in)
- ``JourneyRef`` A unique reference to the planned journey, that this passenger trip (tap in) was matched to using vehicle journey assignment.
- ``Time`` The time for the passenger trip in local time, specifically the departure from ``FromStopPointID`` as recorded by AVL, or the planned departure if no AVL data was recorded for that journey point.
- ``LineNumber`` The line the vehicle journey is scheduled on, e.g. ``300S`` = 300.
- ``LineDirectionCode`` The direction of the line the vehicle journey is scheduled on. e.g. ``1`` or ``2``. There is really no logic in the choice of which of the direction is ``1``, respectfully ``2``.
- ``FromStopPointId`` Where the passenger boarded (tapped in) on the vehicle journey.
- ``ToStopPointId`` Where the passenger alighted from the vehicle journey. This can be observed directly (if the passenger tapped out), or indirectly, if the passenger alighed (without tapping out), and transfered to another public transport service. In the latter, we will assign the stop point closest (in distance) to the passengers next tap in.
- ``FromTimingPointAbbreviation`` The upstream closests timing point from where the passenger boarded (tapped in) on the vehicle journey. If the passenger tapped on at a timing point, this will be assigned.
- ``ToTimingPointAbbreviation`` The downstream closests timing point from where the passenger alighted (tapped out or transfered) on the vehicle journey. If the passenger tapped on at a timing point, this will be assigned.
- ``JourneyPatternRef`` A unique reference to the specific pattern (sequence of stops) that is visited by this scheduled vehicle journey.
- ``FromTimingPointSequenceNumber`` A topological order of the ``JourneyPatternRef`` level. Can be used to build "topological correct" OD-matrices.
- ``ToTimingPointSequenceNumber`` A topological order of the ``JourneyPatternRef`` level. Can be used to build "topological correct" OD-matrices.

### AFC - Timing Point OD Demand Matrices (od-*{line_number}*-*{sample_freq}*-*{export_date}*-*{num_days}*-demand.npy.gz)
Are contrusted from the above dataset for a single line at a time. To avoid very sparse OD-matrices we have used the Timing Point assignments. This reduces the OD-sizes with an order of magnitude. The format on the file name is: od-*{line_number}*-*{sample_freq}*-*{export_date}*-*{num_days}*-demand.npy.gz. 
- `line_number` corresponds the public line designation, however without letters, e.g. 15E will have `line_number` = 15. 
- `sample_freq` is the time frequency in which the trips has been aggregated (summed), e.g. 1H, 15min, 30min.
- `export_date` is the date of export in iso format (YYYYMMDD).
- `num_days` is the number of days exported leading up to `export_date`.

Example: od-15-1H-20201101-60-demand.npy.gz consists of Timing Point OD-demand matrices for 15E, every time step is 1 hour, the time series is ending November 1 2020, and consists of 60 days of data leading up to this date.

The content is a numpy array of size ``T x W x W``, where `T` is number of timestamps, and `W` is the number of timing points on the given line.

The values of ``T`` is available in a seperate file od-*{line_number}*-*{sample_freq}*-*{export_date}*-*{num_days}*-time.npy.gz, and the values of `W` (i.e. the timing point abbreviations) in od-*{line_number}*-*{sample_freq}*-*{export_date}*-*{num_days}*-stops.npy.gz.

The value of the OD-matrix at (*i*,*j*,*k*) is the number of passengers traveling from timing point *j* to *k* at time *i* (boarded at *j* at time *i*). 

### APC - Sensor data from Automatic People Counting system, pre-matached with AFC-data (apc-afc-*{schedule_date}*.csv.gz)
Each row corresponds to one vehicle journey visiting one stop on its route. Only rows from busses with the APC-system is included.

- ``JourneyPointRef`` A unique reference to the planned journey stop at the stop point.
- ``JourneyRef`` A unique reference to the planned journey.
- ``Time`` The time for the passenger trip in local time, specifically the departure from ``FromStopPointID`` as recorded by AVL, or the planned departure if no AVL data was recorded for that journey point.
- ``LineNumber`` The line the vehicle journey is scheduled on, e.g. ``300S`` = 300.
- ``LineDirectionCode`` The direction of the line the vehicle journey is scheduled on. e.g. ``1`` or ``2``. There is really no logic in the choice of which of the direction is ``1``, respectfully ``2``.
- ``StopPointId`` A unique ID of the stop point for the sensor recording. Notice that a journey can in some (relative rare) cases visit the same stop point twice.
- ``ApcBordingCount`` Number of passengers boarding at stop stop point as sensored by the APC system.
- ``ApcAlightingCount`` Number of passengers boarding at stop stop point as sensored by the APC system.
- ``AfcBordingCount`` Number of passengers boarding at stop stop point as sensored by the AFC system.
- ``AfcAlightingCount`` Number of passengers boarding at stop stop point as sensored by the AFC system.
- ``UpstreamTimingPointAbbreviation`` The upstream closests timing point from this stop point. If this stop point is a timing point, this will be assigned.
- ``DownstreamTimingPointAbbreviation`` The upstream closests timing point from this stop point. If this stop point is a timing point, this will be assigned.

### Supply - Time table (data-journey-*xxx*.csv.gz)
