README for the distance-dependence hippocampal network model used in:

Strueber, M., Sauer, J.-F., Jonas, P. and Bartos, M. 
Distance-dependent inhibition facilitates focality of gamma oscillations in the dentate gyrus
Nat. Comm. 8:758, (2017)

Implementation by Michael Strueber, based on a previous interneuron network model designed by
Imre Vida (Vida et al., 2006, Neuron). Implemented and run under the NEURON7.1 and 7.2 
environment.

Basic network structure:
The standard network model contains 200 fast-spiking INs and 800 regular spiking PNs arranged 
on a ring. Neurons are represented as single compartments. INs contain a leak conductance of 
0.13 mS cm-2 and voltage-gated Hodgkin-Huxley-type Na+ and K+ conductances with a resting 
potential of -65 mV. PNs contain passive, voltage-gated Na+, and three voltage-gated K+ 
(delayed rectifier, A-type, and M-type) conductances to imitate the regular spiking behavior 
of hippocampal principal cells. These mechanisms are adapted from Hemond et al. (2008, 
Hippocampus 18, 411–424). They rest at -75 mV. 
INs are connected to 4 of their 8 nearest neighbors via electrical synapses (transcellular 
conductance 0.01 nS). INs are spaced by 50 µm between adjacent cells. Every IN forms inhibitory 
connections with randomly chosen INs and PNs with a mean connectivity of 60 and 80 connections, 
respectively. Connection probability of IN-IN and IN-PN synapses drops with distance in a 
Gaussian manner (standard deviation (SD) = 25 cell-to-cell distances). The network can be 
additionally equipped with excitatory feedback from PNs to INs. In the standard condition, 
excitatory feedback had a mean connectivity of 50 presynaptic PNs per IN and a more narrow 
projection pattern (SD = 10 cell-to-cell distances). 
Synapses are modeled conductance-based with exponential rise and decay phases (Exp2Syn point 
processes) and with properties deferred from experimental data. In the non-distance-dependent 
(noDD) model, unitary synaptic connections have the following properties: 
IN-IN: peak amplitude = 0.2 mS cm-2, rise time constant tauRT = 0.16 ms and decay time constant 
tau = 1.9 ms 
IN-PN: peak amplitude = 0.02 mS cm-2, tauRT = 0.16 ms and tau = 7.0 ms 
PN-IN: peak amplitude = 0.05 mS cm-2, tauRT = 0.2 ms and tau = 1.0 ms 
The reversal potentials are set to Esyn = -55 mV (IN-IN), Esyn = -65 mV (IN-PN) and 
Esyn = 0 mV (PN-IN). Synaptic delays consist of a constant (0.5 ms) plus a variable component 
which scales with connection distance along the circumference of the ring (conduction velocity 
0.25 m s-1). 

Modeling the distance-dependence:
In the distance-dependent (DD) networks, peak amplitude and decay time constant 
change with the distance between pre- and postsynaptic neuron according to the respective DD 
coefficients: 
noDD: a_ddg_II/IE = 0 ms-1 | b_ddtau_II/IE = 0
DD: a_ddg_II = 0.3 ms-1 | b_ddtau_II = 0.5 for IN-IN; a_ddg_IE = 0.5 ms-1 | b_ddtau_IE = 1.2 
for IN-PN. 
There are boundaries to the distance-dependent parameters amplitude and tau to keep values 
in a realistic range. All amplitudes vary between SynG_II/IE for distance = 0 and 0.1*SynG_II/IE
for the largest distances. All tau values vary between Syn_II/IE_decay_0 and maximally
Syn_II/IE_decay_top. The average strength and time course of unitary inhibition critically 
influence network oscillations. Therefore, the DD network is 'balanced' to the noDD network by 
the following steps, which are performed by procedures defined in the net_dd_vectors file: In a 
first step (tau_II/IE_level()), Syn_II/IE_decay_0 is adjusted so that the average tau of all 
inhibitory synapses matches the noDD value of 1.9 ms and 7 ms for IN-IN and IN-PN connections, 
respectively . In a second step (g_level()), SynG_II/IE is chosen to set the average inhibitory 
strength (conductance integral) in DD networks equal to the noDD model. 
To control for the influence of heterogeneity on oscillatory dynamics, every DD network can be
switched to a corresponding 'shuffled' network, containing the same distribution of amplitudes 
and decay time constants as the DD network, but shuffled in a random non-distance-dependent 
fashion.  

Excitatory drives for INs and PNs each:
1. constant current injection, Imu
2. background synaptic drive, BGStim
3. spatial stimulus as current injection, INinj/PNinj
4. spatial stimulus as synaptic events, IN_SIGN/PN_SIGN

Optional outputs of a simulation:
1. time points and cell indices of spikes ('*i/e_raster.tab')
2. membrane voltages, lfp analog, conductance states of all synapses, connection matrices
Output procedures are defined in the 'net_dd_ana.hoc' file.

Composing files of the model:
net_dd_run.hoc - top level file calling all other procedures
net_dd_params.hoc - defining the parameters of the network and the simulation
net_dd_imodel.hoc - defines the IN cell model
net_dd_emodel.hoc - defines the PN cell model
net_dd_procs.hoc - defines essential routines building the network and running the simulation
net_dd_ana.hoc - analysis routines
net_dd_vectors.hoc - generates vectors which define the distance-dependence and the topology 
		     of the network
net_hh_wbsh.mod - Hodgkin-Huxley model of fast-spiking interneurons
gap.mod - gap junction model
kaprox.mod, kdrca1.mod, km.mod, na3n.mod - ion channel models defining a regular spiking cell