# Анализ данных в разработке игр
Отчет по лабораторной работе #2 выполнила:
- Муштей Виолетта Викторовна
- НМТ-232203

Отметка о выполнении заданий (заполняется студентом):

| Задание | Выполнение | Баллы |
| ------ | ------ | ------ |
| Задание 1 | * | 60 |
| Задание 2 | * | 20 |
| Задание 3 | * | 20 |

знак "*" - задание выполнено; знак "#" - задание не выполнено;

Работу проверили:
- к.т.н., доцент Денисов Д.В.
- к.э.н., доцент Панов М.А.
- ст. преп., Фадеев В.О.

[![N|Solid](https://cldup.com/dTxpPi9lDf.thumb.png)](https://nodesource.com/products/nsolid)

[![Build Status](https://travis-ci.org/joemccann/dillinger.svg?branch=master)](https://travis-ci.org/joemccann/dillinger)

Структура отчета

- Данные о работе: название работы, фио, группа, выполненные задания.
- Цель работы.
- Задание 1.
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Задание 2.
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Задание 3.
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Выводы.
- ✨Magic ✨

## Цель работы
Научиться передавать в Unity данные из Google Sheets с помощью Python.

## Задание 1
### Выберите одну из игровых переменных в игре СПАСТИ РТФ: Выживание (HP, SP, игровая валюта, здоровье и т.д.), опишите её роль в игре, условия изменения / появления и диапазон допустимых значений. Постройте схему экономической модели в игре и укажите место выбранного ресурса в ней.
Ход работы:
- Рассмотрим игровую переменную HP.
- Количество здоровья занимает ключевую роль в игре, т.к. при достижении значения 0 игрок проигрывает.
- Здоровье уменьшается в случае получения урона от зомби. Здоровье восстанавливается от навыков: вампиризм и регенерация.
- Диапазон значения здоровья от 0 до 30. Максимальное количество здоровья можно прокачать по ходу игры.
- Схема экономической модели в игре.  
 ![Схема экономической модели в игре](./diagram.png)  
- Роль здоровья в экономической модели: так как потеря здоровья приводит к поражения, задача игрока этого не допустить, для данной цели он должен использовать прокачку и монеты для покупки более сильного оружия и патронов. Если игрок будет игнорировать эти траты, то он достаточно быстро проиграет, из чего можно сделать вывод, что здоровье играет ключевую роль в экономической модели и мотивирует игрока пользоваться всеми ресурсами.

## Задание 2
### С помощью скрипта на языке Python заполните google-таблицу данными, описывающими выбранную игровую переменную в игре “СПАСТИ РТФ:Выживание”. Средствами google-sheets визуализируйте данные в google-таблице (постройте график / диаграмму и пр.) для наглядного представления выбранной игровой величины. Опишите характер изменения этой величины, опишите недостатки в реализации этой величины (например, в игре может произойти условие наступления эксплойта) и предложите до 3-х вариантов модификации условий работы с переменной, чтобы сделать игровой опыт лучше.
Ход работы:
- Для визуализации игровой переменной рассмотрим следующую модель: игрок каждый раунд теряет по (номер раунда * 3) единиц здоровья. Каждый 3-й раунд он прокачивает вампиризм и во время раунда восстанавливает (уровень навыка * 10) здоровья. Каждый 4-й раунд игрок прокачивает регенерацию и во время раунда восстанавливает (уровень навыка * 5) здоровья. И каждый 5-й раунд игрок прокачивает своё максимальное здоровье на +10.
- Скрипт на языке Python:
``` python
import gspread
import numpy as np

gc = gspread.service_account(filename='lab-2-c9198252763c.json')
sh = gc.open('lab-2')
worksheet = sh.sheet1
sh.sheet1.clear() 

damage = 3
vampirism = 10
vampirism_LVL = 0
regeneration = 5
regeneration_LVL = 0
current_HP = 30
max_HP = 30
up_HP = 10
total_rounds = 20

row = 1
sh.sheet1.update_acell('A' + str(row), 'Раунд')
sh.sheet1.update_acell('B' + str(row), 'HP')

for round_number in range(total_rounds):
    row += 1
    current_HP += -damage*(round_number+1) + vampirism*vampirism_LVL + regeneration*regeneration_LVL

    if((round_number+1) % 3 == 0):
        vampirism_LVL += 1
    if((round_number+1) % 4 == 0):
        regeneration_LVL += 1
    if((round_number+1) % 5 == 0):
        max_HP += 10

    if(current_HP < 0):
        current_HP = 0
    if(current_HP > max_HP):
        current_HP = max_HP
    print(vampirism_LVL, regeneration_LVL, max_HP, -damage*round_number + vampirism*vampirism_LVL + regeneration*regeneration_LVL)
    sh.sheet1.update_acell('A' + str(row), str(round_number+1))
    sh.sheet1.update_acell('B' + str(row), str(current_HP))
```
- Визуализация данных:
 ![Схема экономической модели в игре](./HP.png)  
- Как видно из визуализации в начале игроку предоставляется испытание, но чем дальше идёт прокачка, тем легче для него становятся уровни. Это происходит из-за того, что угроза почти не меняется, в то время, как игрок становиться всё сильнее и сильнее. Чтобы это исправить можно добавить новых противников, тем самым заставляя игрока тратить больше здоровья, а значит держать в напряжении. Кроме этого можно добавить возможность обмена здоровья на монеты или улучшения. Также добавление различных модификаторов для локации, которые наносили бы периодический урон, повысило бы ценность здоровья.

## Задание 3
### Настройте на сцене Unity воспроизведение звуковых файлов, описывающих динамику изменения выбранной переменной. Например, если выбрано здоровье главного персонажа вы можете выводить сообщения, связанные с его состоянием.
Ход работы:
- Будем выводить звук основываясь на значении здоровья. До 10 - плохо, от 10 до 30 нормально, от 30 хорошо.
- Скрипт для Unity:
``` C#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.Networking;
using SimpleJSON;

public class NewBehaviourScript : MonoBehaviour
{
    public AudioClip goodSpeak;
    public AudioClip normalSpeak;
    public AudioClip badSpeak;
    private AudioSource selectAudio;
    private Dictionary<string, float> dataSet = new Dictionary<string, float>();
    private bool statusStart = false;
    private int i = 1;

    // Start is called before the first frame update
    void Start()
    {
        StartCoroutine(GoogleSheets());
    }

    // Update is called once per frame
    void Update()
    {
        if (dataSet["Mon_" + i.ToString()] <= 10 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioBad());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }

        if (dataSet["Mon_" + i.ToString()] > 10 & dataSet["Mon_" + i.ToString()] <= 30 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioNormal());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }

        if (dataSet["Mon_" + i.ToString()] > 30 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioGood());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }
    }

    IEnumerator GoogleSheets()
    {
        UnityWebRequest curentResp = UnityWebRequest.Get("https://sheets.googleapis.com/v4/spreadsheets/1O486gPuwBCap3oiDN1dM4uvArGbxThjYO_2dTw5BUWw/values/Лист1?key=AIzaSlJJYHFa5HwHAo3PF6pSMwaie9CwnRkNDdU");
        yield return curentResp.SendWebRequest();
        string rawResp = curentResp.downloadHandler.text;
        var rawJson = JSON.Parse(rawResp);
        foreach (var itemRawJson in rawJson["values"])
        {
            
            var parseJson = JSON.Parse(itemRawJson.ToString());
            var selectRow = parseJson[0].AsStringList;

            dataSet.Add(("Mon_" + selectRow[0]), float.Parse(selectRow[2]));
        }
    }

    IEnumerator PlaySelectAudioGood()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = goodSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(3);
        statusStart = false;
        i++;
    }
    IEnumerator PlaySelectAudioNormal()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = normalSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(3);
        statusStart = false;
        i++;
    }
    IEnumerator PlaySelectAudioBad()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = badSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(4);
        statusStart = false;
        i++;
    }
}
```
- Запуск и проверка:  
![Вывод в консоль](./result.png)  

## Выводы
- Проанализировала экономику игры и предложила нововведения, которые могли бы улучшить пользовательский опыт.
- Научилась генерировать данные с помощью скрипта на python и передавать их в google-таблицу.
- Научилась передавать данные из google-таблицы в Unity и на их основе производить действия .

| Plugin | README |
| ------ | ------ |
| Dropbox | [plugins/dropbox/README.md][PlDb] |
| GitHub | [plugins/github/README.md][PlGh] |
| Google Drive | [plugins/googledrive/README.md][PlGd] |
| OneDrive | [plugins/onedrive/README.md][PlOd] |
| Medium | [plugins/medium/README.md][PlMe] |
| Google Analytics | [plugins/googleanalytics/README.md][PlGa] |

## Powered by

**BigDigital Team: Denisov | Fadeev | Panov**