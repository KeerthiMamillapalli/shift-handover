# shift-handover
import pandas as pd
#from datetime import datetime as dt
#import numpy as np
import streamlit as st
st.set_page_config(layout="wide")
file = st.file_uploader("upload your file",type=['csv'])  #file uploader function
if file is not None:
    df = pd.read_csv(file)   #dataframe
    df['z'] = pd.to_datetime(df['Created Date'], format="%d-%m-%Y %H:%M")
    a = pd.Series([])  #empty column
    df1 = pd.Series([])  #empty data frame
    df['Date'] = df['z'][0]
    df['y'] = pd.to_datetime(df['Date'], format="%d-%m-%Y %H:%M")
    df['Age'] = (df['y'] - df['z']).dt.days

    for i in range(len(df)):
        if df["Age"][i] == 0:
            a[i] = "0-24hrs"

        elif 3 > df["Age"][i] >= 1:
            a[i] = "1-3days"

        else:
            a[i] = ">3days"
    df['Age>3'] = a

    df1 = pd.DataFrame(df.Owner.unique(), columns=['Owner'])
    df2 = pd.DataFrame(df.Priority, columns=['Priority'])
    df1.append(df2, ignore_index=False)
    table = pd.pivot_table(df, values=['Age'], index=['Owner'],
                           columns=['Age>3'], aggfunc='count', fill_value='', margins=True, margins_name='Grand Total')
    tablep = pd.pivot_table(df, values=['Age'], index=['Age>3']
                            , aggfunc='count', fill_value='')
    tablep.plot(kind='bar')
    dff = pd.DataFrame(table)
    table1 = pd.pivot_table(df, values=['TicketID'], index=['Owner'],
                            columns=['Priority'], aggfunc='count', fill_value='', margins=True,
                            margins_name='Grand Total')
    table2 = pd.pivot_table(df, values=['TicketID'], index=['Owner'],
                            columns=['Backlog Status'], aggfunc='count', fill_value='', margins=True,
                            margins_name='Grand Total')

    col1, col2 = st.beta_columns(2)
    col1.header("Priority Wise Report")
    col1.write(table1)
    col2.header("Status Wise Report")
    col2.write(table2)
    col1.header("Age Wise Report")
    col1.write(table)
    st.bar_chart(tablep)

    my_expander = st.beta_expander("Priority Wise Report")
    my_expander.write(table1)
    my_expander = st.beta_expander("Status Wise Report")
    my_expander.write(table1)
    my_expander = st.beta_expander("Age Wise Report")
    my_expander.write(table1)
else:
    st.write("file not uploaded")
