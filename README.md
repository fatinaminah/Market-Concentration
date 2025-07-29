# Market Concentration in Life Insurance Industry: HHI and CR4

This post explores two standard measures for market concentration and competition in an industry: The Herfindahl-Hirschman Index (HHI) and the Concentration Ratio of the 4 largest firms (CR4).

I will explain briefly what the HHI and 4 firm concentration ratio mean, how they are measured, and how I applied these metrics for the life insurance industry in Malaysia using Python. Sample Python code for conventional life insurance is included in this post.

In Malaysia there are two life insurance sectors. They are conventional life insurance market and Islamic (Takaful) life insurance market. The Islamic-based life insurance market has grown rapidly over the past 30 years spurred by the demand for Shariah compliant life insurance products by the Muslim-majority population.

> What is the market concentration of the Conventional and Takaful Life
> Insurance Companies operating in Malaysia?

## Total assets of life insurance companies 

I computed market concentration using total assets as a proxy for company size. I used annual data over 5 years (2011–2015) for 11 conventional life insurance companies and 11 Islamic life insurance (Family Takaful) companies in Malaysia, collected from annual reports and financial statements.


![](https://miro.medium.com/v2/resize:fit:700/1*VAEdc85vq-iLkiYeNYcO5w.png)




![](https://miro.medium.com/v2/resize:fit:700/1*gll0HY3FInGOlrMPKDRHFQ.png)



    import pandas as pd  
    import plotly.graph_objects as go  
      
    # Total assets data  
    data = {  
        "Conventional Firms": [  
            "Allianz Life Insurance Malaysia Berhad",  
            "AmMetlife Insurance Berhad",  
            "American International Assurance Bhd",  
            "Axa Affin Life Insurance Berhad",  
            "Great Eastern Life Assurance Malaysia Berhad",  
            "Prudential Assurance Malaysia Berhad",  
            "Tokio Marine Life Insurance Malaysia Berhad",  
            "Manulife Insurance Berhad",  
            "Hong Leong Assurance Malaysia Berhad",  
            "Gibraltar BSN Life Berhad",  
            "Sun Life Malaysia Assurance Berhad"  
        ],  
        "2011": [4044447, 2941122, 20904375, 881742, 53670119, 16006783, 5495865, 3530656, 7263342, 2296313, 1660183],  
        "2012": [4863322, 2987552, 22607261, 1036240, 59635456, 17981933, 6192536, 3820216, 8238310, 2429724, 1702195],  
        "2013": [5846641, 3186456, 42257664, 908361, 63465353, 20785053, 6672431, 3981298, 10143746, 2300073, 1797062],  
        "2014": [6825449, 3166556, 46138786, 1070101, 66857241, 22417685, 7031336, 4218014, 11739633, 2200725, 2008037],  
        "2015": [7866324, 3152028, 47060510, 1289358, 70062581, 24935850, 7523645, 4462550, 13293195, 2225311, 1991724]  
    }  
      
    # Create DataFrame  
    df_assets = pd.DataFrame(data)  
    df_assets.set_index("Conventional Firms", inplace=True)  
      
    df_assets.loc["Total"] = df_assets.sum()  
    df_display = df_assets.applymap(lambda x: f"{int(x):,}")  
      
    # Create table  
    fig = go.Figure(data=[go.Table(  
        header=dict(  
            values=["<b>Conventional Firms</b>", "<b>2011</b>", "<b>2012</b>", "<b>2013</b>", "<b>2014</b>", "<b>2015</b>"],  
            fill_color='darkgray',  
            font=dict(color='white', size=16),  
            align='center'  
        ),  
        cells=dict(  
            values=[df_display.index] + [df_display[year] for year in df_display.columns],  
            fill_color=[['white']*(len(df_display)-1) + ['#f5f5f5']] * 6,  # Total row shading  
            align=['left', 'center'],  
            font=dict(size=14)  
        )  
    )])  
      
    fig.update_layout(  
        title={  
            'text': "<b>Total Assets of Life Insurance Companies (2011–2015)</b>",  
            'x': 0.5,  
            'xanchor': 'center',  
            'font': dict(size=20)  
        }  
    )  
    fig.show()

## Market shares

First, I computed market shares for each company in each market by dividing each company’s total assets by the total assets from all 11 companies in each market, and multiplying by 100.


![](https://miro.medium.com/v2/resize:fit:700/1*3xtSpQrDzjS402UvfEKH7w.png)



![](https://miro.medium.com/v2/resize:fit:700/1*cdSLhvlnyfZKELHrOc5w_g.png)

The results show that Great Eastern Life Assurance (Malaysia) Berhad from 2011–2015 had the highest market share in the conventional life insurance sector. Meanwhile, AmMetLife Takaful Berhad had the largest market share in the Islamic life insurance market in the same time period from 2011–2015.

    # Calculate market share (%)  
    df_market_share = df_assets.div(df_assets.loc["Total"]) * 100  
    df_market_share = df_market_share.drop("Total")  
      
    # Stacked bar chart for market share  
    df_market_share_T = df_market_share.transpose()  
      
    fig = px.bar(df_market_share_T,  
                 title="Market Share of Conventional Life Insurance Companies (2011–2015)",  
                 labels={"value": "Market Share (%)", "variable": "Institution"},  
                 text_auto=".2f")  
      
    fig.update_layout(  
        barmode='stack',  
        xaxis_title="Year",  
        yaxis_title="Market Share (%)",  
        title={  
            'text': "<b>Market Share of Conventional Life Insurance Companies (2011–2015)</b>",  
            'x': 0.5,  
            'xanchor': 'center',  
            'font': dict(size=20)    
        }  
    )  
      
    fig.show()

## Herfindahl-Hirschman Index (HHI)

Next, I square the market shares of the firms in each market and add them together to calculate the Herfindahl-Hirschman Index (HHI). The HHI has the following interpretation:

_HHI < 1,000: Highly competitive market_

_1,000 ≤ HHI ≤ 1,800: Moderately concentrated market_

_HHI > 1,800: Highly concentrated market_


![](https://miro.medium.com/v2/resize:fit:700/1*1NEGprARe54wN47JR1N4Yw.png)

![](https://miro.medium.com/v2/resize:fit:700/1*xRPorK_TX3mx6LhUW4ETAw.png)

Referring to the above table results, the HHI values for both conventional and Islamic life insurance markets in Malaysia are considered to have high levels of market concentration that fall within the “highly concentrated” category.

    # Calculate market share squared and HHI  
    df_market_share_squared = df_market_share ** 2  
    hhi = df_market_share_squared.sum().round(2)  
      
    # Table of market share squared and HHI  
    table_values = df_market_share_squared.round(2)  
    table_values.loc["HHI"] = hhi  
      
    fig2 = go.Figure(data=[go.Table(  
        header=dict(  
            values=["Conventional Firms"] + list(table_values.columns),  
            fill_color='lightgray',  
            align='center',  
            font=dict(size=16)  
        ),  
        cells=dict(  
            values=[table_values.index] + [table_values[col].tolist() for col in table_values.columns],  
            fill_color=[['white']*(len(df_display)-1) + ['#f5f5f5']] * 6,  # HHI row shading  
            align=['left', 'center'],  
            format=[""] + [".2f"] * 5,  
            font=dict(size=14)  
        )  
    )])  
      
    fig2.update_layout(  
        title={  
            'text': "<b>Market Share Squared and HHI (Conventional Life Insurance)</b>",  
            'x': 0.5,  
            'xanchor': 'center',  
            'font': dict(size=18)  
        }  
    )  
      
    fig2.show()

## Market Concentration Ratio (CR4)

Four-firm concentration ratio (CR4) captures the market shares of the top four firms in a specific industry. It is computed as follows:

CR4 = (Total Assets of four leading firms)/(Total Assets for entire market)  ×100

CR4 Interpretation:

0%: No concentration — Perfect competition

1% to 50%: Low concentration — Monopolistic competition

51% to 80%: Moderate concentration — Monopolistic or oligopolistic competition

81% to 100%: High concentration — Oligopoly or monopoly

Based on total assets, the top four firms in the conventional life insurance sector are: Great Eastern Life Assurance (Malaysia) Berhad, American International Assurance Bhd, Prudential Assurance Malaysia Berhad, and Hong Leong Assurance Berhad., the top four firms in the Islamic life insurance sector are: AmMetLife Takaful Berhad, HSBC Amanah Takaful Sdn Bhd, Etiqa Takaful Berhad, Takaful Malaysia.

![](https://miro.medium.com/v2/resize:fit:700/1*Iq2_Y8Ulj3uRHiItp794Mw.png)

![](https://miro.medium.com/v2/resize:fit:700/1*eR2HwmW8iE2UrZWRQwyDqg.png)

The CR4 values indicate that the top four firms in both conventional and Islamic life insurance hold over 81% of total market assets indicating a highly concentrated market. Given this concentration, there is likely monopoly power as to suggest the dominant firms would potentially have the power to limit the entry of new competitors into the market.

    # Calculate 4-firm concentration ratio (CR4)  
    top_4_firms = [  
        "American International Assurance Bhd",  
        "Great Eastern Life Assurance Malaysia Berhad",  
        "Prudential Assurance Malaysia Berhad",  
        "Hong Leong Assurance Malaysia Berhad"  
    ]  
      
    df_top4 = df_market_share.loc[top_4_firms]  
    CR4 = df_top4.sum().round(2)  
      
    # Convert the CR4 Series to a DataFrame for table display  
    df_cr4_table = CR4.reset_index()  
    df_cr4_table.columns = ["Year", "CR4 (%)"]  
      
    # Create a table using Plotly  
    fig3 = go.Figure(data=[go.Table(  
        header=dict(  
            values=["<b>Year</b>", "<b>CR4 (%)</b>"],  
            fill_color='darkblue',  
            font=dict(color='white', size=16),  
            align='center'  
        ),  
        cells=dict(  
            values=[df_cr4_table["Year"], df_cr4_table["CR4 (%)"]],  
            fill_color='white',  
            align='center',  
            font=dict(size=14)  
        )  
    )])  
      
    fig3.update_layout(  
        title={  
            'text': "<b>4-Firm Concentration Ratio (CR4) – Conventional Life Insurance</b>",  
            'x': 0.5,  
            'xanchor': 'center'  
        }  
    )  
      
    fig3.show()

To conclude, the market structure of both conventional life insurance and Family Takaful in Malaysia is highly concentrated. Nevertheless, the analysis indicates that the Islamic life insurance market experienced slightly higher concentration than the conventional life insurance market, as indicated by both the HHI and CR4 during 2011–2015.
