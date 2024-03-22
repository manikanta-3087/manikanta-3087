import streamlit as st
import pandas as pd
import altair as alt

title = st.container()
body = st.container()

with title:
    text = 'D3.js Integration with Streamlit'
    title = f"""
        <div style='
            font-family: arial;
            text-align: center;
            font-size: 40px;
            font-weight: bold;
        '>{text}
        </div>
        """
    st.markdown(title, unsafe_allow_html=True)

with body:
   uploaded_file = st.file_uploader("Choose a CSV file", type="csv")

   if uploaded_file is not None:
    data = pd.read_csv(uploaded_file)
    data['index'] = data.index  # Add an 'index' column
    graph_type = st.selectbox("Select a type of graph", ["Bar Chart", "Line Chart", "Scatter Plot", "Histogram", "Area Chart", "Heatmap"])
    column_to_display = st.selectbox("Select a column to display", data.columns)
    
    if data[column_to_display].dtype in ['int64', 'float64']:
        min_value = int(data[column_to_display].min())
        max_value = int(data[column_to_display].max())
        slider_value = st.slider("Select a range of values", min_value, max_value, (min_value, max_value))
        data = data[(data[column_to_display] >= slider_value[0]) & (data[column_to_display] <= slider_value[1])]

    if graph_type == "Bar Chart":
        chart = alt.Chart(data).mark_bar().encode(
            x=column_to_display,
            y='count()'
        )
    elif graph_type == "Line Chart":
        chart = alt.Chart(data).mark_line().encode(
            x='index',
            y=column_to_display
        )
    elif graph_type == "Scatter Plot":
        chart = alt.Chart(data).mark_circle().encode(
            x='index',
            y=column_to_display
        )
    elif graph_type == "Histogram":
        chart = alt.Chart(data).mark_bar().encode(
            alt.X(column_to_display, bin=True),
            y='count()'
        )
    elif graph_type == "Area Chart":
        chart = alt.Chart(data).mark_area().encode(
            x='index',
            y=column_to_display
        )
    elif graph_type == "Heatmap":
        column_to_display_2 = st.selectbox("Select another column for Heatmap", data.columns)
        chart = alt.Chart(data).mark_rect().encode(
            x='index:O',
            y=column_to_display_2+':O',
            color=column_to_display+':Q'
        )

    st.altair_chart(chart, use_container_width=True)
