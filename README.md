import numpy as np
import pandas as pd
import csv
import flet as ft
# 讀資料

ticket_df = pd.read_csv('tickets.csv', header=None)
ticket = ticket_df.values
ticket_df
# 南下的組合

southbound_dict = {}
for i in range(len(ticket[0])):
    for j in range(i+1, len(ticket[0])-1):
        station_1 = str(ticket[0][i+1])
        station_2 = str(ticket[0][j+1])
        southbound_dict[(station_1, station_2)] = (ticket[j+1][i+1], ticket[i+1][j+1])
# 北上的組合

northbound_dict = {}
for i in range(len(ticket[0])):
    for j in range(i+1, len(ticket[0])-1):
        station_1 = str(ticket[0][-i-1])
        station_2 = str(ticket[0][-j-1])
        northbound_dict[(station_1, station_2)] = (ticket[-i-1][-j-1], ticket[-j-1][-i-1])
# 站名

station = []
for i in range(1, len(ticket[0])):
    station.append(str(ticket[0][i]))
# 這個function只是給大家感受一下GUI大概會怎麼被使用的樣子。接收到2個input，然後output票價的結果

def THSR_fare():
    start_station = input('Enter your start station:')
    end_station = input('Enter your end station:')
    if southbound_dict.get((start_station, end_station)) is not None:
        print(f'Southbound from {start_station} to {end_station}')
        print(f'The ticket fare of Standard Car is: {southbound_dict[start_station, end_station][0]} TWD')
        print(f'The ticket fare of Business Car is: {southbound_dict[start_station, end_station][1]} TWD')
    elif northbound_dict.get((start_station, end_station)) is not None:
        print(f'Northbound from {start_station} to {end_station}')
        print(f'The ticket fare of Standard Car is: {northbound_dict[start_station, end_station][0]} TWD')
        print(f'The ticket fare of Business Car is: {northbound_dict[start_station, end_station][1]} TWD')
    else:
        print(f'The destination from {start_station} to {end_station} is not found')
def main(page: ft.Page):
    global start_station, end_station

    # GUI的排版
   page.title = "Taiwan High Speed Rail Fare System"
    page.window_width = 750
    page.window_height = 600
    page.horizontal_alignment = ft.CrossAxisAlignment.CENTER
    page.add(ft.Text("Ordinary Tickets"))
    start_station = ""
    end_station = ""

    # 按鈕的function
   def start_station_click(aaa):
        # 使用global變數定義start_station讓其他function也能夠使用到被更新後的同樣參數
        # 可以使用aaa.control.data抓到站名
        global start_station
        start_station = aaa.control.data
        page.add(ft.Text(f"The start station of your choice: {start_station}"))
    
   def end_station_click(aaa):
        global end_station
        end_station = aaa.control.data
        page.add(ft.Text(f"The end station of your choice: {end_station}"))
    
   def result_click(aaa):
        # 可參考THSR_fare()這個function去實作這邊 
        global start_station, end_station
        if start_station != "" and end_station != "":
            if southbound_dict.get((start_station, end_station)) is not None:
                page.add(ft.Text("---Regular Tickets / Adult Tickets---"))
                standard_car_fare = float(southbound_dict[start_station, end_station][0].replace(',', ''))
                business_car_fare = float(southbound_dict[start_station, end_station][1].replace(',', ''))
                page.add(ft.Text(f'The ticket fare of Standard Car is: {standard_car_fare:.0f} TWD'))
                page.add(ft.Text(f'The ticket fare of Business Car is: {business_car_fare:.0f} TWD'))
                page.add(ft.Text("---Concession Tickets (Disabled People, Children, Elder) [50% off]---"))
                concession_tickets_fare_1 = standard_car_fare * 0.5
                concession_tickets_fare_2 = business_car_fare * 0.5
                page.add(ft.Text(f'The ticket fare of Standard Car is: {concession_tickets_fare_1:.0f} TWD'))
                page.add(ft.Text(f'The ticket fare of Business Car is: {concession_tickets_fare_2:.0f} TWD'))
                page.add(ft.Text("---Group Tickets (11 people or more) [5% off]---"))
                group_tickets_fare_1 = standard_car_fare * 0.95
                group_tickets_fare_2 = business_car_fare * 0.95
                page.add(ft.Text(f'The ticket fare of Standard Car is: {group_tickets_fare_1:.0f} TWD', size=12))
                page.add(ft.Text(f'The ticket fare of Business Car is: {group_tickets_fare_2:.0f} TWD', size=12))
            elif northbound_dict.get((start_station, end_station)) is not None:
                page.add(ft.Text("---Regular Tickets / Adult Tickets---"))
                standard_car_fare = float(southbound_dict[start_station, end_station][0].replace(',', ''))
                business_car_fare = float(southbound_dict[start_station, end_station][1].replace(',', ''))
                page.add(ft.Text(f'The ticket fare of Standard Car is: {standard_car_fare:.0f} TWD'))
                page.add(ft.Text(f'The ticket fare of Business Car is: {business_car_fare:.0f} TWD'))
                page.add(ft.Text("---Concession Tickets (Disabled People, Children, Elder) [50% off]---"))
                concession_tickets_fare_1 = standard_car_fare * 0.5
                concession_tickets_fare_2 = business_car_fare * 0.5
                page.add(ft.Text(f'The ticket fare of Standard Car is: {concession_tickets_fare_1:.0f} TWD'))
                page.add(ft.Text(f'The ticket fare of Business Car is: {concession_tickets_fare_2:.0f} TWD'))
                page.add(ft.Text("---Group Tickets(11 people or more) [5% off]---"))
                group_tickets_fare_1 = standard_car_fare * 0.95
                group_tickets_fare_2 = business_car_fare * 0.95
                page.add(ft.Text(f'The ticket fare of Standard Car is: {group_tickets_fare_1:.0f} TWD', size=12))
                page.add(ft.Text(f'The ticket fare of Business Car is: {group_tickets_fare_2:.0f} TWD', size=12))
            else:
                page.add(ft.Text(f'The destination from {start_station} to {end_station} is not found'))

    # ------建立物件------
   start_text = ft.Text("Please select a start station")
    end_text = ft.Text("Please select a end station")
    
    # /// for start station view ///
    # 可以一個一個建立出按鈕，但也可以透過for loop 去建立出 4(column)*3(row)的樣子
    # 使用station這個參數可以抓到各個站名
    # 使用這個函數實作，參數可以自己調整，除了width
    # ft.ElevatedButton(text=f"{station[i]}", data=f"{station[i]}", width=150, on_click=start_station_click)
   start_buttons = ft.Column(
    [
        ft.Row([ft.ElevatedButton(text=f"{station[i]}", data=f"{station[i]}", width=170, on_click=start_station_click)
                for i in range(4)]),
        ft.Row([ft.ElevatedButton(text=f"{station[i]}", data=f"{station[i]}", width=170, on_click=start_station_click)
                for i in range(4, 8)]),
        ft.Row([ft.ElevatedButton(text=f"{station[i]}", data=f"{station[i]}", width=170, on_click=start_station_click)
                for i in range(8, 12)]),
    ]
    )
    
    # /// for end station view ///
    #
   end_buttons = ft.Column(
    [
        ft.Row([ft.ElevatedButton(text=f"{station[i]}", data=f"{station[i]}", width=170, on_click=end_station_click)
                for i in range(4)]),
        ft.Row([ft.ElevatedButton(text=f"{station[i]}", data=f"{station[i]}", width=170, on_click=end_station_click)
                for i in range(4, 8)]),
        ft.Row([ft.ElevatedButton(text=f"{station[i]}", data=f"{station[i]}", width=170, on_click=end_station_click)
                for i in range(8, 12)]),
    ]
    )
    
    # /// for result view ///
    # 使用以下函數實作
    # ft.ElevatedButton(text=f"Calculate the fare", width=630, on_click=result_click)
    # ft.Text("")
   result_button = ft.ElevatedButton(text=f"Calculate the fare", width=700, on_click=result_click)
    result_text = ft.Text("")

    # ------將物件進行排版------
   page.add(start_text,
            start_buttons,
            end_text,
            end_buttons,
            result_button,
            result_text,
            )

ft.app(target=main)
