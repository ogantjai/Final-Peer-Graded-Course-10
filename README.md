# Final-Peer-Graded-Course-10

#Data Collection
https://eu-de.dataplatform.cloud.ibm.com/analytics/notebooks/v2/bf055d92-3635-4f66-a5d9-4e9ed1250ddc/view?access_token=068f29301fd48a826ac1dfff52db59f718f53257ecf7d286b329bbc954ab177b

#Web Scrapping 
https://eu-de.dataplatform.cloud.ibm.com/analytics/notebooks/v2/0b4a85c7-bcbc-4fe8-9b63-03a8b82eead8/view?access_token=0339cdd4ec925bb4dd405ef84288f78bb1d9903f8b951f6f3ac0013614cce53d

#EDA/Data Wrangling 
https://eu-de.dataplatform.cloud.ibm.com/analytics/notebooks/v2/a32d5e36-4de4-4e11-aa9e-9f38b0966902/view?access_token=21fb471f9e960c21b2790a66e4f035ba51f4cd0a1328f6964f182b353ec9c871

#EDA SQL
https://eu-de.dataplatform.cloud.ibm.com/analytics/notebooks/v2/28b39d79-6767-4813-a6f5-b7a636c91fe6/view?access_token=8f2117fccb788b065a9a1000047e2342f2aca2ba6823cec74d6b7b8024ce48fe

#EDA Visualization 
https://eu-de.dataplatform.cloud.ibm.com/analytics/notebooks/v2/21413b2c-8d11-4fb3-9293-fc373f1949c8/view?access_token=ea05858004d51560f53eb6a1bd940f067c1ab82211c0c71b27f1ffab75afc45c

#Machine Learning Program 
https://eu-de.dataplatform.cloud.ibm.com/analytics/notebooks/v2/174a86df-8dbd-4303-881e-feec09a0fd50/view?access_token=8f1b8eef2be063f777710d796c399faedfadce7815c2bd0b239a20653369c179

#Interactive Folium 
https://eu-de.dataplatform.cloud.ibm.com/analytics/notebooks/v2/9440f972-1fc7-4ecd-b4e2-f981d6bb3e5d/view?access_token=3a2102f9b6c5d7035e25ea10f699f57dc704b16f56fd32490a2f4ef02c863580

#Plotly

import pandas as pd
import dash
import dash_html_components as html
import dash_core_components as dcc
from dash.dependencies import Input, Output
import plotly.express as px


spacex_df = pd.read_csv("spacex_launch_dash.csv")
max_payload = spacex_df['Payload Mass (kg)'].max()
min_payload = spacex_df['Payload Mass (kg)'].min()


app = dash.Dash(__name__)


app.layout = html.Div(children=[html.H1('SpaceX Launch Records Dashboard',
                                        style={'textAlign': 'center', 'color': '#503D36',
                                               'font-size': 40}),
                               
                                dcc.Dropdown(id='site-dropdown',
                                            options=[
                                                         {'label': 'ALL SITES', 'value': 'ALL'},
                                                         {'label': 'site1', 'value': 'site1'},
                                                    ],
                                            value='ALL',
                                            placeholder='Select a Launch Site here', 
                                            searchable=True),
                                html.Br(),
                                
                               
                                html.Div(dcc.Graph(id='success-pie-chart')),
                                html.Br(),

                                html.P("Payload range (Kg):"),
                             
                               
                                 dcc.RangeSlider(id='payload-slider',
                                                min=0,max=10000,step=1000,
                                                value=[min_payload,max_payload],
                                                marks={0: '0', 100:'100'}),
                               
                                html.Div(dcc.Graph(id='success-payload-scatter-chart')),
                                ])


@app.callback(
    Output(component_id='success-pie-chart', component_property='figure'),
    Input(component_id='site-dropdown', component_property='value'))

def build_graph(site_dropdown):
    if site_dropdown == 'ALL':
        piechart = px.pie(data_frame = spacex_df, names='Launch Site', values='class' ,title='Total Launches for All Sites')
        return piechart
    else:
        #specific_df = spacex_df['Launch Site']
        specific_df=spacex_df.loc[spacex_df['Launch Site'] == site_dropdown]
        piechart = px.pie(data_frame = specific_df, names='class',title='Total Launch for a Specific Site')
        return piechart

@app.callback(
    Output(component_id='success-payload-scatter-chart', component_property='figure'),
    [Input(component_id='site-dropdown', component_property='value'),
    Input(component_id='payload-slider', component_property='value')])

def update_graph(site_dropdown, payload_slider):
    if site_dropdown == 'ALL':
        filtered_data = spacex_df[(spacex_df['Payload Mass (kg)']>=payload_slider[0])
        &(spacex_df['Payload Mass (kg)']<=payload_slider[1])]
        scatterplot = px.scatter(data_frame=filtered_data, x="Payload Mass (kg)", y="class", 
        color="Booster Version Category")
        return scatterplot
    else:
        specific_df=spacex_df.loc[spacex_df['Launch Site'] == site_dropdown]
        filtered_data = specific_df[(specific_df['Payload Mass (kg)']>=payload_slider[0])
        &(spacex_df['Payload Mass (kg)']<=payload_slider[1])]
        scatterplot = px.scatter(data_frame=filtered_data, x="Payload Mass (kg)", y="class", 
        color="Booster Version Category")
        return scatterplot


if __name__ == '__main__':
    app.run_server()

    

