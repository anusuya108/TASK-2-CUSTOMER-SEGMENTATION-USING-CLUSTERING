# TASK-2-CUSTOMER-SEGMENTATION-USING-CLUSTERING
import pandas as pd import seaborn as sns import matplotlib.pyplot as plt from sklearn.cluster import KMeans import warnings warnings.filterwarnings('ignore')

df=pd.read_excel("Mall_Customers.xlsx") df.head()

# Univariate Analysis
df.describe() sns.distplot(df['Annual Income (k$)'])

columns=['Age','Annual Income (k$)','Spending Score (1-100)'] for i in columns: plt.figure() sns.distplot(df[i])

sns.kdeplot(df['Annual Income (k$)'],shade=True,hue=df['Gender']);

columns=['Age','Annual Income (k$)','Spending Score (1-100)'] for i in columns: plt.figure() sns.kdeplot(df[i],shade=True,hue=df['Gender'])

columns=['Age','Annual Income (k$)','Spending Score (1-100)'] for i in columns: plt.figure() sns.boxplot(data=df,x='Gender',y=df[i]) df['Gender'].value_counts(normalize=True)

# Bivariate Analysis
sns.scatterplot(data=df,x='Annual Income (k$)',y='Spending Score (1-100)')

df=df.drop('CustomerID',axis=1) sns.pairplot(df,hue='Gender')

df.groupby(['Gender'])['Age','Annual Income (k$)', 'Spending Score (1-100)'].mean()

sns.heatmap(df.corr(),annot=True,cmap='coolwarm')

# Clustering - Univariate, Bivariate, Multivariate
clustering1=KMeans() clustering1.fit(df[['Annual Income (k$)']]) clustering1.labels_ df['Income Cluster']=clustering1.labels_ df.head() df['Income Cluster'].value_counts()

clustering1.inertia_

intertia_scores=[] for i in range(1,11): kmeans=KMeans(n_clusters=i) kmeans.fit(df[['Annual Income (k$)']]) intertia_scores.append(kmeans.inertia_) intertia_scores

plt.plot(range(1,11),intertia_scores)

df.groupby('Income Cluster')['Age','Annual Income (k$)', 'Spending Score (1-100)'].mean()

#Bivariate Clustering clustering2=KMeans(n_clusters=5) clustering2.fit(df[['Annual Income (k$)','Spending Score (1-100)']]) df['Spending and Income Cluster']=clustering2.labels_ df.head()

centers=pd.DataFrame(clustering2.cluster_centers_) centers.columns=['x','y'] plt.figure(figsize=(10,8)) plt.scatter(x=centers['x'],y=centers['y'],s=100,c='black',marker='*') sns.scatterplot(data=df,x='Annual Income (k$)',y='Spending Score (1-100)',hue='Spending and Income Cluster',palette='tab10') plt.savefig('clustering_bivariate.png')

pd.crosstab(df['Spending and Income Cluster'],df['Gender'],normalize='index')

df.groupby('Spending and Income Cluster')['Age','Annual Income (k$)', 'Spending Score (1-100)'].mean()

#multivariate clustering from sklearn.preprocessing import StandardScaler scale=StandardScaler() dff=pd.get_dummies(df,drop_first=True) df.head() dff=dff[['Age','Annual Income (k$)','Spending Score (1-100)','Gender_Male']] df.head() dff=scale.fit_transform(dff) dff=pd.DataFrame(scale.fit_transform(dff)) dff.head()

intertia_scores3=[] for i in range(1,11): kmeans3=KMeans(n_clusters=i) kmeans3.fit(dff) intertia_scores3.append(kmeans3.inertia_) plt.plot(range(1,11),intertia_scores3)
