# homeseer

There are two files of interest, ann_learn_rate.py and ann_thermostat_rate.py. The first file bills and trains the neural network and is run at intermittent intervals. The second file is run at frequent intervals (e.g. every five minutes) to calculate the current hitting rate using the neural network and decide whether a heater should be switched on the pending on the current temperature and future temperature targets. In the current debilitation temperature target is given for every hour of every day in the week, and I had a nice JavaScript implementation that allowed me to “draw” the temperature target curve for each day. There are also a bunch of other files in the repository which are basically training data for neural networks and the trained neural networks.

The neural network library I use is pyfann. Documentation should be available online.

Unfortunately, the source code is not very well-documented, but here is a brief overview. We start with ann_learn_rate.py. We start with the addresses (rfxcom) of the temperature sensors and heating switches in the different rooms. All history is stored in a mysql database. For every room would pick out the periods when the heater was on and calculate the length of this period and the total change in temperature from beginning to end. This is the heating rate for the conditions measured at that time. The conditions measured at that time are selected in the heating_start_measurements for loop which basically picks out the state of every device in the system (temperature, humidity sensor, and heating switches) at the beginning of the heating period.

The value of every measurement is thne normalised since the version used of the neural network library did not handle normalisation by itself. This is done below the #scale comment.

The next step is to build training data files based on the measured data, and finally the neural network is built and trained, and written to a file.

The second file, ann_thermostat_rate.py is significantly simpler. It picks out the current state of all the temperature, humidity, and heating switch devices, together with the heating schedule for the next few hours (4). A neural network is created from the file built in the training session, and the measurements (but not the heating schedule) are fed into the neural network. The outcome of the neural network is the heating rate. Finally, based on the heating rate and the heating schedule a decision is returned on whether the heater should be switched on at this time or not (calculate_state).

I’m sorry if this is a bit unclear, but it was the best I could do on short notice. I haven’t worked closely with the internals of openhab, but I believe it should be reasonably simple to develop this as some kind of internal functionality.

The user must in some way define the measurements that should be part of the decisions for a specific neural network, as well as the switch that should be actuated by it in the background the system will gather statistics on the heating periods to store exactly how the heating rate is affected by the values of these measurements at the start of the heating period. A similar background job will regularly train the neural network at some appropriate (Long) interval. Finally, at frequent intervals the trained neural network is executed based on the current measurements.

I would be happy to answer any questions, and if anyone wants to build support for this in openhabin some way (using either pyfann,tensorflow, or anything else) I would be happy to help in any way I can. Anyway, this is a fun discussion :slight_smile:
