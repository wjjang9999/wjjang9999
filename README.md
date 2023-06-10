import requests
region = {
    "충북":"M10",
    "제주":"T10",
}

def get_sc_code(name):
    param = {
        "SCHUL_NM":name,
        "ATPT_OFCDC_SC_CODE":"M10",
        "Type":"json",
    }

    res = requests.get('https://open.neis.go.kr/hub/schoolInfo',params=param)
    data = res.json()
    r = True
    try:
        data = data["schoolInfo"][1]["row"][0]["SD_SCHUL_CODE"]
    except:
        data = "x"
        r = False
    return r,data
def get_meal(code, d):
    params = {
        "ATPT_OFCDC_SC_CODE":"M10",
        "Type":"json",
        "SD_SCHUL_CODE":code,
        "MLSV_YMD":d,
    }
    res = requests.get("https://open.neis.go.kr/hub/mealServiceDietInfo",params=params)
    data = res.json()
    data = data["mealServiceDietInfo"][1]["row"][0]["DDISH_NM"]
    data = data.split("<br/>")
    newData = []
    for m in data:
        newData.append(m.split("  ")[0])
    return newData

import PySimpleGUI as gs

layout = [
    [gs.Text("학교이름 입력.")], [gs.Input("생명초", key="-scname-")],
    [gs.Text("날짜")], [gs.Input("2023", key="-date-")],
    [gs.Multiline("데이터 없음", key = "-txt-", expand_x= True, size= (0,7))],
    [gs.Button("찾기", key="-버튼1-",expand_x=True)],

]

window = gs.Window("CBEI",layout)
while True:
    event, values = window.read()
    print(event,values)
    if event == gs.WIN_CLOSED:
        break
    if event == "-버튼1-":
        scname = values["-scname-"]
        d = values["-date-"]
        r, data = get_sc_code(scname)
        if r == True:
            meals = get_meal(data,d)
            window["-txt-"].update("\n".join(meals))
window.close()
