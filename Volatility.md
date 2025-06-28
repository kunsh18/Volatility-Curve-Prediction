# Importing Required Libraries
    import numpy as np
    import pandas as pd
    import matplotlib.pyplot as plt
    from sklearn.preprocessing import StandardScaler
    from sklearn.decomposition import PCA
    import xgboost as xgb
#  Data Sourcing
    train_df= pd.read_parquet(f'D:\\OneDrive\\Documents\\Skill up\\LLM\\nk-iv-prediction\\train_data.parquet')
    test_df = pd.read_parquet(f'D:\\OneDrive\\Documents\\Skill up\\LLM\\nk-iv-prediction\\test_data.parquet')
# Data Cleaing
    train_df=train_df.dropna()
    test_df = test_df.dropna()
    train_df['call_iv_26000']=train_df.loc[: ,'call_iv_23500':'call_iv_25900'].mean(axis=1)
    train_df=train_df.drop('expiry',axis=1)
    features=train_df.loc[:, 'X0':'X41']

    scaler=StandardScaler()
    features_scaled_ary=scaler.fit_transform(features)
    features_scaled=pd.DataFrame(features_scaled_ary,columns=features.columns,index=features.index)
# Principal Component Analysis
    pca=PCA(n_components=None)
    pca.fit(features)
    explained_variance_ratio = pca.explained_variance_ratio_
    cumulative_explained_variance = np.cumsum(explained_variance_ratio)
    plt.figure(figsize=(10, 6))
    plt.plot(range(1, len(cumulative_explained_variance) + 1), cumulative_explained_variance, marker='^', linestyle='--',color='red')
    plt.axhline(y=0.96, color='r', linestyle=':', label='96% Variance Threshold')
    plt.axhline(y=0.92, color='g', linestyle=':', label='92% Variance Threshold')
    plt.axhline(y=0.78, color='b', linestyle=':', label='78% Variance Threshold')
    plt.axvline(x=4, color='b', linestyle=':', label='No of component')
    plt.xlabel('Number of components')
    plt.ylabel('Cumulative explained variance')
    plt.legend()
    pca=PCA(n_components=4) #Retains 96% of total variability
    feature_2d=pca.fit_transform(features)
    feature_df=pd.DataFrame(data=feature_2d,columns=['X0','X1','X2','X3'])
#  Model Selection and Training
    for i in range(train_df.shape[0]):
    underList=pd.DataFrame([underlying.iloc[i]]*len(call_iv_value),index=call_iv_value.index)
    featureList=pd.DataFrame([feature_df.loc[i, 'X0':'X3']]*len(call_iv_value),index=call_iv_value.index)

    call_y=pd.Series(call_outPut.iloc[i]).reset_index(drop=True)

    put_y=pd.Series(put_outPut.iloc[i]).reset_index(drop=True)

    call_final_trainSet=pd.concat([underList,featureList,call_iv_value],axis=1)

    gb_regressor.fit(call_final_trainSet,call_y)
    call_testSet=pd.concat([underList,featureList,call_test_iv_value],axis=1)

    call_y_pred=pd.DataFrame(gb_regressor.predict(call_testSet))

    put_final_trainSet=pd.concat([underList,featureList,put_iv_value],axis=1)
    gb_regressor.fit(put_final_trainSet,put_y)
    put_y_pred=pd.DataFrame(gb_regressor.predict(pd.concat([underList,featureList,put_test_iv_value],axis=1)))
    call_y_pred=call_y_pred.T
    put_y_pred=put_y_pred.T
    call_y_pred.columns=test_df.loc[:, 'call_iv_24000':'call_iv_26500'].columns.to_list()
    put_y_pred.columns=test_df.loc[:, 'put_iv_23000':'put_iv_25500'].columns.to_list()
    if i==0:
        final_Output=pd.concat([call_y_pred,put_y_pred],axis=1)
    else:
        final_Output=pd.concat([final_Output,pd.concat([call_y_pred,put_y_pred],axis=1)],axis=0,ignore_index=True)
    if i==12067:
        break
    print(final_Output.head())
# Train and Predict
    for i in range(train_df.shape[0]):
    underList=pd.DataFrame([underlying.iloc[i]]*len(call_iv_value),index=call_iv_value.index)
    featureList=pd.DataFrame([feature_df.loc[i, 'X0':'X3']]*len(call_iv_value),index=call_iv_value.index)

    call_y=pd.Series(call_outPut.iloc[i]).reset_index(drop=True)

    put_y=pd.Series(put_outPut.iloc[i]).reset_index(drop=True)

    call_final_trainSet=pd.concat([underList,featureList,call_iv_value],axis=1)

    gb_regressor.fit(call_final_trainSet,call_y)
    call_testSet=pd.concat([underList,featureList,call_test_iv_value],axis=1)

    call_y_pred=pd.DataFrame(gb_regressor.predict(call_testSet))

    put_final_trainSet=pd.concat([underList,featureList,put_iv_value],axis=1)
    gb_regressor.fit(put_final_trainSet,put_y)
    put_y_pred=pd.DataFrame(gb_regressor.predict(pd.concat([underList,featureList,put_test_iv_value],axis=1)))
    call_y_pred=call_y_pred.T
    put_y_pred=put_y_pred.T
    call_y_pred.columns=test_df.loc[:, 'call_iv_24000':'call_iv_26500'].columns.to_list()
    put_y_pred.columns=test_df.loc[:, 'put_iv_23000':'put_iv_25500'].columns.to_list()
    if i==0:
        final_Output=pd.concat([call_y_pred,put_y_pred],axis=1)
    else:
        final_Output=pd.concat([final_Output,pd.concat([call_y_pred,put_y_pred],axis=1)],axis=0,ignore_index=True)
    if i==12067:
        break
    print(final_Output.head())
