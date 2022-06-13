'''
Komplexwertiger Aufbau
Autor: Johannes Müller
'''
import numpy as np
import pandas as pd
import math
import matplotlib.pyplot as plt
import itertools
import random

import tensorflow as tf

import time


def ls_estimator(x, y):
    penrose_inv = np.linalg.pinv(np.matmul(x, np.matrix.getH(x)))
    other_part = np.matmul(y, np.matrix.getH(x))
    H_LS = np.matmul(other_part, penrose_inv)
    return H_LS

def reg_ls_estimator(x, y):
    reg = 0.0001*np.eye(num_tx)
    penrose_inv = np.linalg.pinv(np.matmul(x, np.matrix.getH(x))+reg)
    other_part = np.matmul(y, np.matrix.getH(x))
    H_LS = np.matmul(other_part, penrose_inv)
    return H_LS

def lmmse_estimator(var_n, var_h, num_tx, num_rx, num_pilot, x, y, r):
    r_hh = var_h*np.eye(num_tx)
    r_nn = var_n*np.eye(num_pilot)
    if r==1:
        r_nn = var_n*num_rx*np.eye(num_pilot)
    lmmse_inv = np.linalg.pinv(np.matmul(np.matmul(np.matrix.getH(x), r_hh), x)+r_nn)
    H_LMMSE = np.matmul(y, np.matmul(np.matmul(lmmse_inv, np.matrix.getH(x)), r_hh))
    return H_LMMSE  

def re_imag_seperation(data_in):
    real = np.asarray(data_in.real)
    imag = np.asarray(data_in.imag)
    return real, imag

def recover_re_imag(np_real, np_imag):
    output = np_real + 1j*np_imag
    return output

def signal_generation(num_samples, var_s, var_n, var_h, num_rx, num_tx, num_pilot, train_gen):
    y_list = []
    x_list = []
    H_list = []
    H_LS_list = []
    H_LS_REG_list = []
    H_LMMSE_list = []
    H_LMMSE_list_r = []
    for index in range(num_samples):
        if train_gen == 1:
            var_n = var_s/(float(10**(random.uniform(0.001, 25)/10)))
            var_h = var_h
        #Signalgeneration
        x = np.random.normal(0, np.sqrt(var_s), size=(num_tx, num_pilot*2)).view(np.complex128)
        noise = np.random.normal(0, np.sqrt(var_n), size=(num_rx, num_pilot*2)).view(np.complex128)  
        H = np.random.normal(0, np.sqrt(var_h), (num_rx, num_tx*2)).view(np.complex128)  
        y = np.matmul(H, x) + noise
        
        y_list.append(np.asarray(y).flatten())
        x_list.append(np.asarray(x).flatten())
        H_list.append(np.asarray(H).flatten())
        
        # Vorschätzung LS
        H_LS = ls_estimator(x, y)
        H_LS_REG = reg_ls_estimator(x, y)
        H_LS_list.append(np.asarray(H_LS).flatten())
        H_LS_REG_list.append(np.asarray(H_LS_REG).flatten())
        
        #Schätzung LMMSE
        H_LMMSE = lmmse_estimator(var_n, var_h, num_tx, num_rx, num_pilot, x, y, r=0)
        H_LMMSE_list.append(np.asarray(H_LMMSE).flatten())
        H_LMMSE_r = lmmse_estimator(var_n, var_h, num_tx, num_rx, num_pilot, x, y, r=1)
        H_LMMSE_list_r.append(np.asarray(H_LMMSE_r).flatten())
    
    #improve:
    y_list = np.asarray(y_list)
    x_list = np.asarray(x_list)
    y_x_list = np.concatenate((y_list, x_list), axis=1)
    H_list = np.asarray(H_list)
    H_LS_list = np.asarray(H_LS_list)
    H_LS_REG_list = np.asarray(H_LS_REG_list)
    H_LMMSE_list = np.asarray(H_LMMSE_list)
    H_LMMSE_list_r = np.asarray(H_LMMSE_list_r)
    return H_list, H_LS_list, H_LS_REG_list, H_LMMSE_list, y_list, y_x_list, x_list, H_LMMSE_list_r 


start_time = time.time()

# Settings to scan
num_tx_list = [2, 4, 8]
num_rx_list = [1, 2, 4, 8]
rel_num_pilot_num_tx = list(np.arange(0, 9, 1))
rel_var_s_var_n_in_dB = [0.001] + list(np.arange(2, 22, 2))

settings = itertools.product(num_tx_list, num_rx_list, rel_num_pilot_num_tx)

var_s = 1/2
var_h = 1/2
var_n = np.nan
num_samples = 100000

doc_results = []
for item in settings:
    num_rx = item[1]
    num_tx = item[0]
    num_pilot = num_tx+item[2]  
    
    #Trainingsamples generation
    matrix_select = 2
    train_samples = signal_generation(int(num_samples*3/4), var_s, var_n, var_h, num_rx, num_tx, num_pilot, train_gen=1)
    x_train_ri = train_samples[matrix_select]
    y_train_ri = train_samples[0]
    x_train_re, x_train_imag = re_imag_seperation(x_train_ri)
    y_train_re, y_train_imag = re_imag_seperation(y_train_ri)
    
    in_dim=num_tx*num_rx
    out_dim=in_dim
    
    batch_factor = 10
    def create_model():
        model = tf.keras.models.Sequential()
        model.add(tf.keras.layers.InputLayer(input_shape = (in_dim,)))
        for i in range(0, 2): #https://keras-team.github.io/keras-tuner/
            model.add(tf.keras.layers.Dense(units = 40, activation='relu'))
        model.add(tf.keras.layers.Dense(out_dim, activation='linear'))
        optimizer = tf.keras.optimizers.Adam()
        model.compile(loss="mse", optimizer=optimizer)
        return model
    model_re = create_model()
    model_imag = create_model()
    callbacks_list = [tf.keras.callbacks.EarlyStopping(monitor='val_loss', patience= 30)]
    
    model_re.fit(x_train_re, y_train_re, validation_split=0.5, batch_size= int(num_samples/batch_factor), epochs=5000, verbose=0,  callbacks=callbacks_list)
    model_imag.fit(x_train_imag, y_train_imag, validation_split=0.5, batch_size= int(num_samples/batch_factor), epochs=5000, verbose=0,  callbacks=callbacks_list)    
    
    for snr_index in rel_var_s_var_n_in_dB:
        var_n = var_s/(10**(snr_index/10)) 
        #Testsamples generation
        test_samples = signal_generation(int(num_samples*1/4), var_s, var_n, var_h, num_rx, num_tx, num_pilot, train_gen=0)
        
        x_test_ri = test_samples[matrix_select]
        x_test_re, x_test_imag = re_imag_seperation(x_test_ri)
        re_out_nn=model_re.predict(x_test_re)
        imag_out_nn=model_imag.predict(x_test_imag)
        H_ls_reg_nn = recover_re_imag(re_out_nn, imag_out_nn)
    
        #evaluate performance (metrics):
        #https://dsp.stackexchange.com/questions/35808/matlab-what-is-the-proper-way-to-calculate-mean-square-error-for-complex-number
        mse_ls = np.mean(abs(test_samples[0]-test_samples[1])**2)
        mse_ls_reg = np.mean(abs(test_samples[0]-test_samples[2])**2)
        mse_ls_reg_nn = np.mean(abs(test_samples[0]-H_ls_reg_nn)**2)
        mse_lmmse = np.mean(abs(test_samples[0]-test_samples[3])**2)
        mse_lmmse_r = np.mean(abs(test_samples[0]-test_samples[-1])**2)

        SNR = 10*math.log10(var_s/var_n)
        doc_results.append([SNR, num_tx, num_rx, num_tx + item[2], num_samples, mse_ls, mse_ls_reg, mse_ls_reg_nn, mse_lmmse, mse_lmmse_r, time.time() - start_time])
        doc_results_df = pd.DataFrame(doc_results, columns=["SNR", "num_tx", "num_rx", "num_pilot", "num_samples", "mse_ls", "mse_ls_reg", "mse_ls_reg_nn", "mse_lmmse", "mse_lmmse_r", "Uptime"])
        doc_results_df.to_excel("doc_results.xlsx")
        print("Runtime: --- %s seconds ---" % (time.time() - start_time))
        
    plt.semilogy(doc_results_df.iloc[:, 0], doc_results_df.iloc[:, 6])
    plt.semilogy(doc_results_df.iloc[:, 0], doc_results_df.iloc[:, 7])
    plt.semilogy(doc_results_df.iloc[:, 0], doc_results_df.iloc[:, 8])
    plt.legend(['LS','NN','MMSE'])
    


