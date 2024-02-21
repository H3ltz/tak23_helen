"""
Isikukoodi klass (model)
"""
from datetime import datetime, date

from Hospital import Hospital


class PersonalCode:
    def __init__(self, personal_code):
        # TODO Siia defineeri vajalikud klassisisesed muutujad hiljem ka GETTERID!
        self.__personal_code = None  # Algselt isikukoodi pole
        self.__gender_nr = None  # Sugu numbrina
        self.__gender_str = None  # Sugu tekstina
        self.__year = None  # Aasta kahekohalisena (int)
        self.__year_full = None  # Aaste neljakohalisena (int)
        self.__birthdate = None  # kuupäev objektina
        self.__hospital_name = None  # see on str, nt Pärnu haigla
        self.__hospital_code = None  # see on int, nt 754
        self.__hospital_codes = None  # see on str, nt 001-293
        self.__child_no = None  # Lapse järjekorra number

        if len(personal_code.strip()) == 11 and personal_code.isdigit():
            self.__personal_code = personal_code.strip()  # Siit saadakse midagi isikukoodiks
            if self.is_gender():  # kas on sugu, kui jah siis näitab tähe ja numbriga
                self.set_year()  # aasta seadistamine
                if self.is_date():
                    if self.is_valid():
                        self.set_hospital_info()
                        print(self.__personal_code)
                        print(self.calculate_age())
                        print(self.get_month_name())
                    else:
                        raise ValueError('Isikukoid on vigane (kontroll nr)')
                else:
                    raise ValueError('Kuupäev on vigane')
                #print(self.__gender_str)  # Naine
                #print(self.__gender_nr)  # 4
                #print(self.__year)  # Lühike aasta
                #print(self.__year_full)  # Pikk aasta
                #print(self.__birthdate) #objektina sünniaeg
                #print(self.is_valid())
                #print(self.calculate_age())
                #print('Haigla ja laps:', self.__hospital_name, self.__child_no)
                #print('Nädalapäev:' , self.get_weekday())
                #print('Kuu:', self.get_month_name())
            else:  # kui ei ole sugu, siis annab veateate
                raise ValueError(f'Vigane sugu')
        else:  # On sisestatud
            raise ValueError(f'See isikukood ei ole õige: {personal_code}')

    # GETTERS
    @property
    def personal_code(self):
        return self.__personal_code

    @property
    def gender_nr(self):
        return self.__gender_nr

    @property
    def gender_str(self):
        return self.__gender_str

    @property
    def birthdate(self):
        return self.__birthdate

    @property
    def hospital_name(self):
        return self.__hospital_name

    @property
    def hospital_code(self):
        return self.__hospital_code

    @property
    def hospital_codes(self):
        return self.__hospital_codes

    @property
    def child_no(self):
        return self.__child_no

    @staticmethod
    def get_hospitals():
        result = [Hospital('001-010', 'Kuressaare haiga'),
                  Hospital('011-019', 'Tartu Ülikooli Naistekliinik'),
                  Hospital('021-150', 'Ida-Tallinna keskhaigla, Pelgulinna sünnitusmaja (Tallinn)'),
                  Hospital('151-160', 'Keila haigla'),
                  Hospital('161-220', 'Rapla haigla, Loksa haigla, Hiiumaa haigla (Kärdla)'),
                  Hospital('221-270', 'Ida-Viru keskhaigla (Kohtla-Järve, endine Jõhvi)'),
                  Hospital('271-370', 'Maarjamõisa kliinikum (Tartu), Jõgeva haigla'),
                  Hospital('371-420', 'Narva haigla'),
                  Hospital('421-470', 'Pärnu haigla'),
                  Hospital('471-490', 'Haapsalu haigla'),
                  Hospital('491-520', 'Järvamaa haigla (Paide)'),
                  Hospital('521-570', 'Rakvere haigla, Tapa haigla'),
                  Hospital('571-600', 'Valga haigla'),
                  Hospital('601-650', 'Viljandi haigla'),
                  Hospital('651-700', 'Lõuna-Eesti haigla (Võru), Põlva haigla'),
                  Hospital('701-990', 'Välismaalane')]

        return result

    def is_gender(self):  # sugu meetod
        gender = int(self.__personal_code[0])  # sugu tehakse numbriks (int) ja IK numbrist 0 loetakse.

        if 1 <= gender <= 6:  # kui esimene number on 1 või suurem, 6 ja väiksem, siis väljastab TRUE
            self.set_gender(gender)  # alumine meetod set_gender kutsutakse välja soo numbriga
            return True
        return False

    def set_gender(self, gender):
        self.__gender_nr = gender
        if gender % 2 == 0:  # kui on paarisarv, siis naine, %2 jäägiga jagamine ehk kui jääki pole = naine, kui jääb jääk = mees. 3%2=1, ehk mees
            self.__gender_str = 'Naine'
        else:
            self.__gender_str = 'Mees'

    def set_year(self):
        self.__year = int(self.__personal_code[1:3])  # 3 ei ole k.a ehk võetakse IKst 2 ja 3 number
        if self.__gender_nr < 3:  # 1-2
            self.__year_full = 1800 + self.__year
        elif self.__gender_nr < 5:  # 3-4
            self.__year_full = 1900 + self.__year
        elif self.__gender_nr < 7:  # 5-6
            self.__year_full = 2000 + self.__year

    def is_date(self):
        month = self.__personal_code[3:5]
        day = self.__personal_code[5:7]
        date_str = str(self.__year_full) + '-' + month + '-' + day  # YYYY-MM-DD

        try:
            bool(datetime.strptime(date_str, '%Y-%m-%d').date())
            self.__birthdate = datetime.strptime(date_str, '%Y-%m-%d').date()
            result = True
        except ValueError:
            result = False

        #print(date_str)
        return result

    def is_valid(self):
        # defineeri muutuja, mille algväärtus on False (isikukood on vale)
        result = False
        # list, mis sisaldab esimese ringi numbrid (I astme kaal)
        kaal1 = [1, 2, 3, 4, 5, 6, 7, 8, 9, 1]
        # Teine list, mis sisaldab teise ringi numbreid (II astmwe kaal)
        kaal2 = [3, 4, 5, 6, 7, 8, 9, 1, 2, 3]
        # defineeri muutjua, mille algväärtus on 0 (siin on summa).
        total = 0

        # käi esimene list läbi isikukoodi iga numbriga ja korruta I astme väärtuse vastava
        # indeksiga ja tulemus liida kokku

        for x in range(len(kaal1)):
            total += kaal1[x] * int(self.__personal_code[x])

        # Arvuta jääk > % 11
        reminder = total % 11

        # kui jääk on 10, siis
        if reminder == 10:
            # käi teine list läbi isikukoodi iga numbriga korruta ja liida kokku
            total = 0
            for x in range(len(kaal2)):
                total += kaal2[x] * int(self.__personal_code[x])

            reminder = total % 11

        # arvuta jääk
        if reminder == int(self.__personal_code[-1]):
            result = True

        return result

    def calculate_age(self):  # objekt self sulgude sees
        today = date.today()
        age = today.year - self.__birthdate.year - (
                    (today.month, today.day) < (self.__birthdate.month, self.__birthdate.day))

        return age

    def set_hospital_info(self):
        hospitals = self.get_hospitals()
        hospital_nr = int(self.__personal_code[7:10])  # IK koodist viimased numbrid, mis int-iks teha
        for hospital in hospitals:
            if hospital.get_start <= hospital_nr <= hospital.get_end:  # kontrollime haigla numbrit, IK peab olema startist suurem, endist väiksem
                self.__hospital_name = hospital.get_name
                #stringina:
                self.__hospital_code = self.__personal_code[7:10]
                self.__hospital_codes = hospital.get_codes
                self.get_child_number(hospital.get_start, hospital.get_end, hospital_nr)

    def get_child_number(self, start, end, hospital_nr):
        if hospital_nr == end:
            self.__child_no = 1
            return 1
        else:
            self.__child_no = (hospital_nr - start) + 1
            return (hospital_nr - start) + 1


    # tagastama eesti keelse nime kp põhjal - kuu või nädalapäeva nime
    def get_weekday(self):
        weekdays = ['Esmaspäev', 'Teisipäev', 'Kolmapäev', 'Neljapäev', 'Reede', 'Laupäev', 'Pühapäev']
        return weekdays[self.__birthdate.weekday()]

    def get_month_name(self):
        months = [' ', 'Jaanuar', 'Veebruar', 'Märts', 'Aprill', 'Mai', 'Juuni', 'Juuli', 'August', 'September', 'Oktoober', 'November', 'Detsember']
        return months[self.__birthdate.month]

        #tühik kuu ees, kuna index algab 0-st


        ______



        from tkinter import ttk, Text, W, END, Scrollbar, EW


class View(ttk.Frame):  # Vaade on Frame mitte main window!
    def __init__(self, parent):
        super().__init__(parent)  # Frame jaoks

        # https://stackoverflow.com/questions/54476511/setting-background-color-of-a-tkinter-ttk-frame
        # https://sites.google.com/view/paztronomer/blog/basic/python-colors
        # NB! self on Frame ise antud kontekstis
        self.style = ttk.Style()
        self.style.configure('TFrame', background='lemonchiffon')

        # Silt (Label)
        self.label = ttk.Label(self, text="Sisesta isikukood", background='lemonchiffon')
        self.label.grid(row=1, column=0)

        # Sisestuskast (Entry)
        self.personal_code = ttk.Entry(self)
        self.personal_code.focus() #sisestusaken kohe aktiivne
        self.personal_code.grid(row=1, column=1)

        # Nupp (Button)
        self.button = ttk.Button(self, text="Kontrolli", command=self.button_clicked)
        self.button.grid(row=1, column=2, padx=5, pady=5)

        # Veateade
        self.message_label = ttk.Label(self, text='', foreground='red', background='lemonchiffon')
        self.message_label.grid(row=4, column=0, padx=5, pady=5, columnspan=3, sticky=W)

        # Mitme realine tekstikast (Text) millel on allservas kerimise riba horisontaalis
        self.result = Text(self, width=40, wrap='none')

        # Järgnevad kolm rida on horisnotaalse kerimisriba jaoks "kasti" all servas
        self.vsb = Scrollbar(self, orient='horizontal', command=self.result.xview)
        self.result.configure(xscrollcommand=self.vsb.set)
        self.vsb.grid(row=3, column=0, columnspan=3, sticky=EW)

        self.result.grid(row=2, column=0, columnspan=3, padx=5, pady=5)  # Paneb tekstikasti framele

        # Vaikimisi kohe controllerit pole
        self.controller = None

    def set_controller(self, controller):
        """
        Meetod kontrolleri tegemiseks/loomiseks
        :param controller:
        :return:
        """
        self.controller = controller

    def button_clicked(self):
        """
        Halda nupu klikkimise sündmust. Handle button click event.
        :return:
        """
        if self.controller:
            self.controller.check(self.personal_code.get())

    def show_error(self, message):
        """
        Show an error message
        :param message:
        :return:
        """
        self.message_label['text'] = message
        self.message_label['foreground'] = 'red'
        self.message_label.after(3000, self.hide_message)

    def show_success(self, message):
        """
        Show a success message
        :param message:
        :return:
        """
        self.message_label['text'] = message
        self.message_label['foreground'] = 'green'
        self.message_label.after(3000, self.hide_message)

    def hide_message(self):
        """
        Hide the message
        :return:
        """
        self.message_label['text'] = ''

    def show_result(self, pc):
        """
        Siin näitab õige isikukoodi korral infot. Kaasa on antud isikukoodi objekt, kust saab vajalikud info kätte
        :param pc:
        :return:
        """
        self.result.delete('1.0', END)  # Tühjenda tulemuse kast
        self.result.insert(END, 'Isikukood: ' + pc.personal_code + '\n')  # Lisa isikukood tulemus kasti, getterid ka vajalikud, END lisab alati lõppu
        self.result.insert(END, 'Sünniaeg: ' + pc.birthdate.strftime('%d.%m.%Y') + '\n')
        self.result.insert(END, 'Sünniaeg lühike: ' + pc.birthdate.strftime('%d.%m.%y') + '\n')
        self.result.insert(END, 'Päev: ' + pc.birthdate.strftime('%d') + '\n')
        self.result.insert(END, 'Kuu ' + pc.birthdate.strftime('%m') + '\n')
        self.result.insert(END, 'Kuu nimi:' + pc.get_month_name() + '\n')
        self.result.insert(END, 'Lühike aasta: ' + pc.birthdate.strftime('%y') + '\n')
        self.result.insert(END, 'Aasta: ' + pc.birthdate.strftime('%Y') + '\n')
        self.result.insert(END, 'Nädalapäeva nr: ' + str(pc.birthdate.weekday()) + '\n')
        self.result.insert(END, 'Nädalapäev: ' + pc.get_weekday() + '\n')
        self.result.insert(END, 'Soo nr: ' + str(pc.gender_nr) + '\n')
        self.result.insert(END, 'Sugu: ' + pc.gender_str + '\n')
        self.result.insert(END, 'Vanus: ' + str(pc.calculate_age()) + '\n')
        self.result.insert(END, 'Haigla nimi: ' + pc.hospital_name + '\n')
        self.result.insert(END, 'Haigla kood: ' + pc.hospital_code + '\n')
        self.result.insert(END, 'Haigla koodid: ' + pc.hospital_codes + '\n')
        self.result.insert(END, 'Järjekorra number: ' + str(pc.child_no) + '\n')
        self.result.insert(END, 'Kontrollkood ' + pc.personal_code[-1] + '\n') #viimane number

        self.personal_code.delete(0, END)  # Tühjenda isikukoodi SISESTUS kast

