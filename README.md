# oceanographic_section_shapes_area

## Скрипты расчёта мощности затока атлантических вод как площади фигуры на карте распределения характеристики на океанографическом разрезе.

Работа выполнялась в рамках [проекта, поддержанного РНФ, № 17-14-01268 "Биология арктического планктона в зоне полярного фронта"](http://www.mmbi.info/rnf_plankton/#%D0%93%D0%BB%D0%B0%D0%B2%D0%BD%D0%B0%D1%8F).

С постановкой задачи, детальным алгоритмом решения и первой версией стека технологий (на базе Matlab, Java и VBA с API Surfer Scripter) можно познакомиться в [тезисах](https://drive.google.com/file/d/1RX0jfKOg2ehiJdeVD5f_IGiID5ocsuLr/view?usp=sharing) и [статье](https://drive.google.com/file/d/15q9kqaZ6EprxA88KLb5TNRhnMLp0XGVe/view?usp=sharing)(разрез "Кольский меридиан"). Основная проблема - плохо документированный Scripter, необходимость кастомизированных визуализаций, которую не поддерживал Scripter, желание использовать только свободное ПО и легкомодифицируемые скрипты на одном языке программирования. Обновленная версия на базе Python представлена в [статье](https://drive.google.com/file/d/1Hc-08rlLEOEh0Pdlfu-XuGNqzukTyMtZ/view?usp=sharing), в файлах AreaCalculator_fjord.ipynb (разрез в Ис-Фьорде, Шпицберген) и AreaCalculator_KolaMeridian.ipynb (разрез "Кольский меридиан") содержатся фрагменты для случая обработки одного входного файла в соответствии с этой версией.

![Превью](https://github.com/ZifRD/norms_anomalies_TS/blob/master/pics/Area.png)

### AreaCalculator_fjord.ipynb
Площади фигур, ограниченных изолиниями (изохалина 34,8 psu), поверхность воды и дном для пространственного распределения солёности по данным экспедиции 2018 года, разрез в Ис-Фьорде (Шпицберген).

1. Подготовка данных: загрузка полигона дна и распределения значений. Следует задать несколько линий уровня и построить их, пользуясь каким-то методом интерполяции (например, кригингом, как сделано в коде). Эти линии позволят понять направления градиентов, чтобы верно учитывать площади со знаком плюс или минус для формирования итоговой суммы.

2. Отображение фрагментов изолиний по очереди вместе с полигоном водной толщи (прямоугольник разреза, из которого удалён полигон дна): основная мысль - строить их пересечения и считать площади частей для каждого фрагмента. Трудность, которая не позволяет полностью автоматизировать процесс: одна изолиния, прерываемая дном, поверхностью или границами разреза по геогр. координатам, не формирует замкнутую ломаную (в том числе может быть представлена несколькими фрагментами). На рисунке изображён полигон толщи воды и две изолинии 34,8 psu - замкнутая и незамкнутая.

![Превью](https://github.com/ZifRD/oceanographic_section_shapes_area/blob/master/pics/IS_poly.png)

После доопределения множества точек ломаной (для того, чтобы замкнуть) имеем следующие две фигуры для исходной незамкнутой изолинии.

![Превью](https://github.com/ZifRD/oceanographic_section_shapes_area/blob/master/pics/IS_shapes.PNG)

3. Нахождение итоговой площади и построение финального рисунка. Используя clabel объекта axis с manual = True можно в интерактивном режиме указать позицию отображения подписи для изолинии. Черным показаны точки измерений.

![Превью](https://github.com/ZifRD/oceanographic_section_shapes_area/blob/master/pics/IS_result.PNG)

### AreaCalculator_KolaMeridian.ipynb
Площади фигур, ограниченных изолиниями (изохалина 34.97 psu), поверхность воды и дном для пространственного распределения солёности по данным экспедиции весны 2018 года, разрез "Кольский меридиан". 

Действуем по аналогии. В этом случае всего одна необходимая изолиния, но разделённая на две. 

![Превью](https://github.com/ZifRD/oceanographic_section_shapes_area/blob/master/pics/Kola_poly.PNG)

Придётся склеивать их и доопределять замкнутую линию. Итоговое изображение.

![Превью](https://github.com/ZifRD/oceanographic_section_shapes_area/blob/master/pics/Kola_result.PNG)

В первом скрипте для доступа к фрагментам изолиний используется от CS = ax.contour вариант CS.allsegs[i][j], а во втором - развёрнутый вариант CS.collections[i].get_paths()[j] (работают аналогично).
