# ForeignKey-va-ManyToMany-bog-lanishlar
Django modellaridagi bog'lanishlar (ForeignKey, ManyToManyField) va ularni boshqarish bo'yicha to'liq amaliy kod va misollarni tayyorladim.

Tasavvur qilaylik, bizda Author (Muallif), Tag (Teg) va Post (Maqola) modellari mavjud.

1. models.py (Modellar va bog'lanishlar)
Ilovangizning models.py faylida modellarni quyidagicha yozasiz:

Python
from django.db import models

class Author(models.Model):
    ism = models.CharField(max_length=100, verbose_name="Muallif ismi")

    def __str__(self):
        return self.ism

class Tag(models.Model):
    nomi = models.CharField(max_length=50, verbose_name="Teg nomi")

    def __str__(self):
        return self.nomi

class Post(models.Model):
    title = models.CharField(max_length=200, verbose_name="Sarlavha")
    
    # 1. ForeignKey bog'lanish (on_delete va related_name bilan)
    muallif = models.ForeignKey(
        Author, 
        on_delete=models.CASCADE, 
        related_name='postlar', 
        verbose_name="Muallif"
    )
    
    # 2. ManyToManyField bog'lanish (ko'pga-ko'p)
    teglar = models.ManyToManyField(
        Tag, 
        related_name='postlar', 
        verbose_name="Teglar"
    )

    def __str__(self):
        return self.title
2. Migratsiya qilish (makemigrations va migrate)
Modellar tayyor bo'lgach, ularni bazaga o'tkazish uchun terminalda quyidagi buyruqlarni bajarasiz:

Bash
# Migratsiya fayllarini yaratish
python manage.py makemigrations

# Ma'lumotlar bazasiga qo'llash (migratsiya qilish)
python manage.py migrate
3. related_name orqali teskari tomondan yozuvlarni olish
Model bog'lanishlari yaratilgandan so'ng, related_name='postlar' yordamida muallifga tegishli barcha postlarni juda oson olish mumkin.

Buni python manage.py shell orqali yoki view ichida sinab ko'rishingiz mumkin:

Python
from myapp.models import Author, Post

# Ma'lum bir muallifni topamiz
muallif = Author.objects.get(id=1)

# related_name orqali shu muallifning barcha postlarini olamiz
muallifning_postlari = muallif.postlar.all()
print(muallifning_postlari)
4. Bog'langan jadval maydoni bo'yicha filter ishlatish (__ sintaksisi)
Ikki jadval bog'langandan keyin, ikkita pastki chiziq (__) yordamida bog'langan jadval ustunlari bo'yicha qidirish va filtrlash mumkin:

Python
from myapp.models import Post

# Muallifining ismi 'Ali' bo'lgan barcha postlarni filtrlash
ali_postlari = Post.objects.filter(muallif__ism='Ali')

# Muallif ismi 'Vali' bo'lgan va sarlavhasida 'Django' so'zi bor postlar
django_postlari = Post.objects.filter(muallif__ism='Vali', title__icontains='Django')

# Teg nomi bo'yicha postlarni filtrlash (ManyToManyField orqali)
python_teg_postlari = Post.objects.filter(teglar__nomi='Python')
Shu tarzda modellararo bog'lanishlarni o'rnatib, ularni osongina boshqarishingiz va so'rovlar (queries) yozishingiz mumkin!
