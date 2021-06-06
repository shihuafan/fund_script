import random
import requests
import demjson
import matplotlib.pyplot as plt


def getFundData(code: str, start: str, end: str):
    url = "http://fund.eastmoney.com/f10/F10DataApi.aspx?type=lsjz&code={}&sdate={}&edate={}" \
        .format(code, start, end)
    response = requests.request("GET", url)
    raw = response.text[12:-1]
    raw_json = demjson.decode(raw)
    pages = raw_json['pages']
    data_arr = []
    for i in range(1, pages + 1):
        url = "http://fund.eastmoney.com/f10/F10DataApi.aspx?type=lsjz&code={}&page={}&sdate={}&edate={}" \
            .format(code, i, start, end)
        response = requests.request("GET", url)
        raw = response.text[12:-1]
        raw_json = demjson.decode(raw)
        content = raw_json['content'][183:-16]
        content = content.replace("<tr>", "")
        content = content.replace("</tr>", "")
        content = content.replace("<td>", "")
        content = content.replace("<td class='tor bold'>", "")
        content = content.replace("<td class='tor bold red'>", "")
        content = content.replace("<td class='tor bold grn'>", "")
        content = content.replace("<td class='red unbold'>", "")
        content_data = content.split("</td>")[:-1]
        l = len(content_data)
        j = 0
        while j + 6 < l:
            data_arr.append({"data": content_data[j],
                             "money1": float(content_data[j + 1]),
                             "money2": float(content_data[j + 2]),
                             "present": content_data[j + 3]})
            j += 7
    data_arr.reverse()
    return data_arr


# mode=-1表示最小，-2最大，-3随机，大于等于0表示指定天数
def invest(res_arr: [], crycle: int, mode: int):
    if len(res_arr) < crycle:
        return 0
    share = 0
    value_arr = []
    for res in res_arr:
        value_arr.append(res["money1"])
    inverst_index_arr = []
    inverst_arr = []
    i = 0
    l = len(res_arr)
    while i + crycle <= l:
        m_index = get_invest_index(res_arr, crycle, i, mode)
        m = res_arr[m_index]["money1"]
        inverst_index_arr.append(m_index)
        inverst_arr.append(m)
        i += crycle
        share += 1 / m
    profit = share * res_arr[-1]["money1"] / (len(inverst_arr) * 1) - 1
    return profit

    # plt.plot(value_arr)
    # plt.plot(inverst_index_arr, inverst_arr, 'r .')
    # plt.show()
    # print(share)


def get_invest_index(res_arr: [], crycle: int, start: int, mode: int):
    if mode >= 0:
        return start + mode
    elif mode == -3:
        return random.randint(start, start + crycle - 1)
    elif mode == -1:
        min_money = res_arr[start]["money1"]
        min_index = start
        for i in range(start, start + crycle):
            if min_money > res_arr[i]["money1"]:
                min_money = res_arr[i]["money1"]
                min_index = i
        return min_index
    else:
        max_money = res_arr[start]["money1"]
        max_index = start
        for i in range(start, start + crycle):
            if max_money < res_arr[i]["money1"]:
                max_money = res_arr[i]["money1"]
                max_index = i
        return max_index


def main():
    code = "007412"
    start = "2020-11-24"
    end = "2021-06-06"

    print("基金代码 {}, 开始日期 {}, 截止日期 {}".format(code, start, end))
    res_arr = getFundData(code, start, end)
    crycle_arr = [5, 10, 20, 30, 50, 100]
    print("\t\t\t\t净值最低投资\t\t净值最高投资\t\t随机\t\t\t\t周期第一天买入\t\t")
    for crycle in crycle_arr:
        max_profit = invest(res_arr, crycle, -1)
        min_profit = invest(res_arr, crycle, -2)
        random_profit = invest(res_arr, crycle, -3)
        first_profit = invest(res_arr, crycle, -3)
        print("{}个交易日\t\t{:.2%}\t\t\t{:.2%}\t\t\t{:.2%},\t\t\t{:.2%}".format(crycle, max_profit, min_profit,
                                                                             random_profit, first_profit))


if __name__ == "__main__":
    main()
