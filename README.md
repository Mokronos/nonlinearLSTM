# nonlinearLSTM
Trying to train LSTMs for non-linear models.

## read stuff
- http://karpathy.github.io/2015/05/21/rnn-effectiveness/ done
    - really good introduction to sequential models
    - mostly focused on language models, however many general tips
- https://www.researchgate.net/publication/349681865_Deep_Learning_with_Real_Data_and_the_Chaotic_Double_Pendulum mostly done
    - pretty similar to my work, good reference for code examples
- https://towardsdatascience.com/lstm-for-time-series-prediction-de8aeb26f2ca done
    - good explanation of inner workings of lstm cells
    - nice example with lstm regression
- https://thenerdstation.medium.com/how-to-unit-test-machine-learning-code-57cf6fd81765
    - some general tests one can run to maybe catch some bugs in your machine learning pipeline 
    - wanted to make it a habit to unit test my stuff more, as it is essential with bigger projects
    - should help with bug fixing too
- https://www.mathworks.com/help/deeplearning/ug/sequence-to-sequence-regression-using-deep-learning.html
- https://towardsdatascience.com/a-long-short-term-memory-network-to-model-nonlinear-dynamic-systems-72f703885818
    - pretty much exactly what i want
    - author remarks that when not giving the ground truth at every time step as input (making prediction window longer) the results get way worse, as errors add up
- https://www.researchgate.net/publication/327356232_Non-linear_system_modeling_using_LSTM_neural_network://www.researchgate.net/publication/327356232_Non-linear_system_modeling_using_LSTM_neural_networks 
    - says that LSTMs don't work well when modeling nonlinear systems
    - but their method works better
- https://www.researchgate.net/publication/318333660_A_new_concept_using_LSTM_Neural_Networks_for_dynamic_system_identification
    - last paper cited this as an example of LSTMs not working well for nonlinear modeling
- https://ieeexplore.ieee.org/stamp/stamp.jsp?arnumber=9105007
- https://cpb-us-e1.wpmucdn.com/campuspress-test.yale.edu/dist/7/677/files/2016/09/paper_LSTM_2017ACC-1sz2yir.pdf
- https://sci-hub.se/10.1007/s40435-020-00673-4 , A LSTM based prediction model for nonlinear dynamical systems with chaotic itinerancy
    

- a lot of literature for language models (predicting letter after letter) --> classification
- but most things can be applied for lstm regression as well
## Create data from classic models
- create nonlinear state space models
    - given models work (heat model/ transmission)
    - more general solution better (just plug function and parameters/initials in --> train network)
    - but mostly done
- add measuring noise at output to simulate real world data acquisition
    - done, but only necessary to add later, when no other bugs
- create training and test datasets
    - done, but not in general
    - input: 0 --> n-1, [batch size, sequence length, number of features] (batch size = number of different sequences)
    - labels: 1 --> n
    - easy to just increase/change the number of features to add inputs from ode (current, change of first value, system input, etc.)
    
## Create LSTM
- create train/test functions and data pipeline
    - test/train basics done, need to adjust some stuff for regression
- create simple LSTM with pytorch
    - done? 0,1,2,3 --> 1,2,3,4 (linear) should be really simple to learn
    - loss not going down quickly enough, even for dummy data
    - either stupid bug, or model setup/change from classification to regression not complete
- visualize results
    - TODO, after loss goes down (faster)
## Expand
- expand to more variations of classic models
- different activation functions (changes non-linearity)
- change datasets to better reflect real world (distributions of inputs)


## Architecture

### Input, Output

- input: 0 --> n, [batch size, sequence length (number of time steps) , number of features at every time step] (batch size = number of different sequences)
    - features: inputs of dynamic systems (current/voltage or mechanical force)
    - how to encode initial conditions? Give them as inputs, then make them 0
- output: 0 --> n, any number of values the function would predict

A system is only time dependent (time variant), if the system (not the input) varies in time.

- example: learn y(t) = sin(t) + u with u = [0]*n as input !!! sin(t) not time variant
    - so learn simple sine function and set input to 0
    - sin is nonlinear and depends on previous time steps as the gradient has two options at almost every point
- example: y(t) = t*u


So we have multiple situations:
- many-to-many: every time step has a input value
- one-to-many: some or only the first time step has a input value, e.g. Initial value of a sine curve

