<!---
lvglzun/lvglzun is a ✨ special ✨ repository because its `README.md` (this file) appears on your GitHub profile.
You can click the Preview link to take a look at your changes.
--->
#include <iostream>
#include <conio.h>
#include <stdio.h>
#include <ctime>
#include <errno.h>

using namespace std;
//Объявление типа employee
struct employee
{
    char surname[20];
    char name[20];
    char patronymic[20];
    char position[20];
    char start_date[11];
    float salary;
};
//Добавление одной записи в бинарный файл. Параметры – указатель на файл и имя файла
void add_record( char* name)
{
    employee z; FILE* f;
    //Открыть файл и проверить успешность открытия
    errno_t err;
    if ((err = fopen_s(&f, name, "ab")) != 0)
        printf("File not open.\n");
    //Считать запись с клавиатуры (данные вводятся в строку через табуляцию)
    scanf_s("%s\t%s\t%s\t%s\t%s\t%f", z.surname,20, z.name,20, z.patronymic,20, z.position,20,
        z.start_date,11, &z.salary);
    //Записать запись в файл
    fwrite(&z, sizeof(employee), 1, f);
    //закрыть файл
   fclose(f);
}
//Просмотр файла. Параметры – указатель на файл и имя файла
void view_file(char* name)
{
    employee z; FILE* f;
    errno_t err;
    if ((err = fopen_s(&f, name, "rb")) != 0)
        printf("File not open.\n");
    cout<<"\nView file"<<endl;

    cout<<"Surname\tName\tPatronymic\tPosition\tStart date\tSalary\n"<<endl;
    //Считывать последовательно записи из файла, пока они не закончатся
    while (fread(&z, sizeof(employee), 1, f))
    {
        //Вывести запись на экран в строку через табуляцию
        printf("%s\t%s\t%s\t%s\t%s\t%6.2f\n", z.surname, z.name, z.patronymic,
            z.position, z.start_date, z.salary);
    }
  fclose(f);
}
//Определение количества записей в файле. Параметры – указатель на файл и имя файла
// Результат – целое число (количество записей)
int number_of_records1(char* name)
{
    int n; FILE* f;
    errno_t err;
    if ((err = fopen_s(&f, name, "rb")) != 0)
        printf("File not open.\n");
    
        //Установить курсор в конец файла (0 – сместиться на 0 байт, 2 – от конца файла)
        fseek(f, 0, 2);
    //Определить текущее положение (на сколько байт от начала файла смещён курсор) и
    //разделить на размер одной записи
    n = ftell(f) / sizeof(employee);
    fclose(f);
    return n;
}
//Второй способ определения количества записей в файле.
//Параметры – указатель на файл и имя файла. Результат – целое число (количество записей)
int number_of_records2(char* name)
{
    int n = 0; employee z; FILE* f;
    errno_t err;
    if ((err = fopen_s(&f, name, "rb")) != 0)
        printf("File not open.\n");
    
    //Считывать по очереди все записи и увеличивать счётчик на 1
    while (fread(&z, sizeof(z), 1, f))
        n++;
    fclose(f);
    return n;
}
//Заполнение (создание) массива из бинарного файла.
//Параметры – массив, указатель на файл, имя файла.
void create_array(employee* a, int kol, char* name)
{
    FILE* f;
    errno_t err;
    if ((err = fopen_s(&f, name, "rb")) != 0)
        printf("File not open.\n");
   
    //Считать kol записей из файла в массив а одной командой
    fread(a, sizeof(employee), kol, f);
   fclose(f);
}
//Сортировка массива
void sort_array(employee* a, int kol)
{
    int i; employee x; bool f;
    //Цикл пока массив не отсортирован. Признаком того, что массив уже отсортирован будет
    //значение переменной f, равное 0, после очередного прохода по массиву
    do {
        f = 0;
        //В каждом проходе по массиву сравниваются два соседних элемента. Они меняются местами,
        //если стоят в неправильном порядке
        for (i = 0; i < kol - 1; i++)
            if (a[i].salary < a[i + 1].salary)
            {
                x = a[i]; a[i] = a[i + 1]; a[i + 1] = x;
                f = 1;
            }
    } while (f);
}
//Вывод динамического массива на экран
void view_array(employee * a, int kol)
{
    cout << "Surname\tName\tPatronymic\tPosition\tStart date\tSalary\n" << endl;
    for (int i = 0; i < kol; i++)
        printf("%s\t%s\t%s\t%s\t%s\t%10.2f\n", a[i].surname, a[i].name, a[i].patronymic, a[i].position, a[i].start_date, a[i].salary);
}
//Создание бинарного файла из динамического массива (возвращение данных в файл)
void array_to_file(employee* a, int kol, char* name)
{
    FILE* f;
    errno_t err;
    if ((err = fopen_s(&f, name, "wb")) != 0)
        printf("File not open.\n");
    fwrite(a, sizeof(employee), kol, f);
    fclose(f);
}
//Вывод данных по условию
void select_data(char* name)
{
    employee z; FILE* f;
    time_t t, t1; struct tm tmstr;
    double seconds, yearinseconds = 365.25 * 24 * 60 * 60;
    t = time(NULL);
    errno_t err;
    if ((err = fopen_s(&f, name, "rb")) != 0)
        printf("File not open.\n");
    cout << "\nSelect records" << endl;
    cout << "Surname\tName\tPatronymic\tPosition\tStart date\tSalary\n" << endl;
    while (fread(&z, sizeof(employee), 1, f))
    {
        //Разбиваем строку на части по заданному формату: 3 целых числа (%d), разделённых точками
        //и записываем их в поля структуры tmstr день, месяц и год
        sscanf_s(z.start_date, "%d.%d.%d", &tmstr.tm_mday, &tmstr.tm_mon, &tmstr.tm_year);
        //Отнимаем 1 от месяца и 1900 от года, полям со временем присваиваем нули
        tmstr.tm_mon -= 1; tmstr.tm_year -= 1900; tmstr.tm_hour = 0; tmstr.tm_min = 0; tmstr.tm_sec = 0;
        //Преобразуем во время типа time_t и вычисляем разницу дат в секундах
        t1 = mktime(&tmstr);
        seconds = difftime(t, t1);
        //Если разница во времени больше 5 лет (в секундах) и должность - «инженер»
        if (seconds > yearinseconds * 5 && strcmp(z.position, "Engineer") == 0)
            //Выводим запись на экран
            printf("%s\t%s\t%s\t%s\t%s\t%6.2f\n", z.surname, z.name, z.patronymic, z.position,
                z.start_date, z.salary);
    }
    fclose(f);
}
//Создание текстового файла
void create_text_file(char* bname, char* tname)
{
    employee z; float sum = 0, avg; FILE* fb; FILE* ft;
    int k = number_of_records1(bname);
    //Открываем бинарный файл для чтения
    errno_t err;
    if ((err = fopen_s(&fb, bname, "rb")) != 0)
        printf("File not open.\n");
    //Проходим по бинарному файлу и считаем суммарную зарплату
    while (fread(&z, sizeof(employee), 1, fb))
        sum += z.salary;
    //Вычисляем среднюю зарплату как отношение суммарной зарплаты и количества записей
    avg = sum / k;
    //Переходим в начало файла, чтобы снова по нему пройти
    fseek(fb, 0, 0);
    //Открываем текстовый файл для записи
   
    if ((err = fopen_s(&ft, tname, "wt")) != 0)
        printf("File not open.\n");
    //Идём по бинарному файлу и считываем записи
    while (fread(&z, sizeof(employee), 1, fb))
    {
        //Если запплата ниже средней, то вставляем запись в текстовый файл
        if (z.salary < avg)
            fprintf(ft, "%s\t%s\t%s\t%s\t%s\t%g\n", z.surname, z.name, z.patronymic,
                z.position, z.start_date, z.salary);
    }
    fclose(ft); fclose(fb);
}
//Просмотр текстового файла
void view_text_file(char* name)
{
    employee z; FILE* f;
    errno_t err;
    if ((err = fopen_s(&f, name, "rt")) != 0)
        printf("File not open.\n");
    cout << "\nView text file" << endl;
    cout << "Surname\tName\tPatronymic\tPosition\tStart date\tSalary\n" << endl;
    while (!feof(f))
    {
        fscanf_s(f, "%s\t%s\t%s\t%s\t%s\t%f\n", z.surname,20, z.name,20, z.patronymic,20, z.position,20,
            z.start_date,11, &z.salary);
        printf("%s\t%s\t%s\t%s\t%s\t%6.2f\n", z.surname, z.name, z.patronymic,
            z.position, z.start_date, z.salary);
    }
    fclose(f);
}
//Главная функция
int main()
{
   char name[] =  "kursfile.txt";
   char tname[] = "textfile.txt"; //имена файлов
    //Ввод данных в бинарный файл
    cout<<"Input information about employee:"<<endl;
    cout<<"Surname\tName\tPatronymic\tPosition\tStart date\tSalary\n"<<endl;
    for(int i=0; i<3; i++)
       add_record(name);
    //Вывод файла на экран
    view_file(name);
    //Определение количества записей в файле
    int kol = number_of_records1(name);
    cout<<"Number of records in the file: " << kol << endl;
    //cout<<"Number of records in the file: "<<number_of_records2(name)<<endl;
    //Объявление динамического массива
    employee* a = new employee[kol];
    //Заполнение динамического массива значениями
    create_array(a, kol, name);
    //Вывод динамического массива на экран
    cout << "Array:" << endl;
    view_array(a, kol);
    //Сортировка массива
    sort_array(a, kol);
    //Вывод динамического массива на экран
    cout<<"\nSorted array:"<<endl;
    view_array(a, kol);
    //Возвращение отсортированных данных в бинарный файл
    array_to_file(a, kol, name);
    //Очистка памяти из-под массива
    delete[]a;
    //Вывод файла на экран
    view_file(name);
    //Вывод данных из фала на экран по условию
    select_data(name);
    //Создание текстового файла из бинарного
    create_text_file(name, tname);
    //Вывод содержимого теекстового
    view_text_file(tname);
    system("pause");
    return 0;
}
