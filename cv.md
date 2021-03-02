# CURRICULUM VITAE #

##### 1. Datos personal #####
- Name: Artsem
- Surname: Pronovich
- Phone number: 80295086669
- Email: therestin2@gmail.com
- Telegram: @lilrestinforever

##### 2. Personal information #####
I am 19 years old, I have a profession of a programmer, I want to learn the programming language JavaScript and Python. I am a goal-oriented person, punctual, I have a desire for new things.
##### 3. Skills #####
- Python
  - framework: Django
- .NET/C#
  - WinForm, LINQ, SQL, Xamarin

##### 4. Code examples #####
```python
from django.db import models
from datetime import date

from django.urls import reverse


class Category(models.Model):
    """Категории"""
    name = models.CharField("Категория", max_length=150)
    description = models.TextField("Описание")
    url = models.SlugField(max_length=160, unique=True)

    def __str__(self):
        return self.name

    class Meta:
        verbose_name = "Категория"
        verbose_name_plural = "Категории"


class Actor(models.Model):
    """Актеры и режиссеры"""
    name = models.CharField("Имя", max_length=100)
    age = models.PositiveSmallIntegerField("Возраст", default=0)
    description = models.TextField("Описание")
    image = models.ImageField("Изображение", upload_to="actors/")

    def __str__(self):
        return self.name

    def get_absolute_url(self):
        return reverse('actor_detail', kwargs={"slug": self.name})

    class Meta:
        verbose_name = "Актеры и режиссеры"
        verbose_name_plural = "Актеры и режиссеры"


class Genre(models.Model):
    """Жанры"""
    name = models.CharField("Имя", max_length=100)
    description = models.TextField("Описание")
    url = models.SlugField(max_length=160, unique=True)

    def __str__(self):
        return self.name

    class Meta:
        verbose_name = "Жанр"
        verbose_name_plural = "Жанры"


class Movie(models.Model):
    """Фильм"""
    title = models.CharField("Название", max_length=100)
    tagline = models.CharField("Слоган", max_length=100, default='')
    description = models.TextField("Описание")
    poster = models.ImageField("Постер", upload_to="movies/")
    year = models.PositiveSmallIntegerField("Дата выхода", default=2019)
    country = models.CharField("Страна", max_length=30)
    directors = models.ManyToManyField(Actor, verbose_name="режиссер", related_name="film_director")
    actors = models.ManyToManyField(Actor, verbose_name="актеры", related_name="film_actor")
    genres = models.ManyToManyField(Genre, verbose_name="жанры")
    world_premiere = models.DateField("Примьера в мире", default=date.today)
    budget = models.PositiveIntegerField("Бюджет", default=0,
                                         help_text="указывать сумму в долларах")
    fees_in_usa = models.PositiveIntegerField(
        "Сборы в США", default=0, help_text="указывать сумму в долларах"
    )
    fess_in_world = models.PositiveIntegerField(
        "Сборы в мире", default=0, help_text="указывать сумму в долларах"
    )
    category = models.ForeignKey(
        Category, verbose_name="Категория", on_delete=mode s.SET_NULL, null=True
    )
    url = models.SlugField(max_length=130, unique=True)
    draft = models.BooleanField("Черновик", default=False)

    def __str__(self):
        return self.title

    class Meta:
        verbose_name = "Фильм"
        verbose_name_plural = "Фильмы"


class MovieShots(models.Model):
    """Кадры из фильма"""
    title = models.CharField("Заголовок", max_length=100)
    description = models.TextField("Описание")
    image = models.ImageField("Изображение", upload_to="movie_shots/")
    movie = models.ForeignKey(Movie, verbose_name="Фильм", on_delete=models.CASCADE)

    def __str__(self):
        return self.title

    class Meta:
        verbose_name = "Кадр из фильма"
        verbose_name_plural = "Кадры из фильма"


class RatingStar(models.Model):
    """Звезда рейтинга"""
    value = models.SmallIntegerField("Значение", default=0)

    def __str__(self):
        return f'{self.value}'

    class Meta:
        verbose_name = "Звезда рейтинга"
        verbose_name_plural = "Звезды рейтинга"
        ordering = ["-value"]


class Rating(models.Model):
    """Рейтинг"""
    ip = models.CharField("IP адрес", max_length=15)
    star = models.ForeignKey(RatingStar, on_delete=models.CASCADE, verbose_name="звезда")
    movie = models.ForeignKey(Movie, on_delete=models.CASCADE, verbose_name="фильм", related_name="ratings")

    def __str__(self):
        return f"{self.star} - {self.movie}"

    class Meta:
        verbose_name = "Рейтинг"
        verbose_name_plural = "Рейтинги"


class Reviews(models.Model):
    """Отзывы"""
    email = models.EmailField()
    name = models.CharField("Имя", max_length=100)
    text = models.TextField("Сообщение", max_length=5000)
    parent = models.ForeignKey(
        'self', verbose_name="Родитель", on_delete=models.SET_NULL, blank=True, null=True
    )
    movie = models.ForeignKey(Movie, verbose_name="фильм", on_delete=models.CASCADE)

    def __str__(self):
        return f"{self.name} - {self.movie}"

    class Meta:
        verbose_name = "Отзыв"
        verbose_name_plural = "Отзывы"
```
```python
from django.shortcuts import render, redirect
from django.views.generic import ListView, DetailView
from django.views.generic.base import View

from .models import Movie
from .forms import ReviewForm


class MoviesView(ListView):
    """Список фильмов"""
    model = Movie
    queryset = Movie.objects.filter(draft=False)


class MovieDetailView(DetailView):
    """Полное описание фильма"""
    model = Movie
    slug_field = "url"


class AddReview(View):
    """Отзывы"""
    def post(self, request, pk):
        form = ReviewForm(request.POST)
        movie = Movie.objects.get(id=pk)
        if form.is_valid():
            form = form.save(commit=False)
            form.movie = movie
            form.save()
        return redirect("/")
        #Movie.get_absolute_url()
```
```html
{% extends 'movies/base.html' %}
{% block title %} {{ movie.title }} {% endblock title %}
{% block header %} bg1 {% endblock header %}
{% block container %}
<div class="container py-md-3">
    {% endblock container %}

    {% block sidebar %}
    <div class="side-bar col-lg-4">
        {% endblock sidebar %}

        {% block movie %}
        <div class="left-ads-display col-lg-8">
            <div class="row">
                <div class="desc1-left col-md-6">
                    <img src="{{ movie.poster.url }}" class="img-fluid" alt="">
                </div>
                <div class="desc1-right col-md-6 pl-lg-4">
                    <h3 class="editContent" style="outline: none; cursor: inherit;">
                        {{ movie.title }}
                    </h3>
                    <h5 class="editContent"></h5>
                    <ul>
                        <li style="list-style: none">
                            <span><b>Год:</b> {{ movie.year }}</span></li>
                        <li style="list-style: none">
                            <span><b>Страна:</b> {{ movie.country }}</span>
                        </li>
                        <li style="list-style: none">
                            <span><b>Слоган:</b> {{ movie.tagline }}</span>
                        </li>
                        <li style="list-style: none">
                                    <span><b>Режиссер:</b>
                                        {% for director in movie.directors.all %}
                                            {{ director.name }}
                                        {% endfor %}
                                    </span>
                        </li>
                        <li style="list-style: none"><span><b>Актеры:</b>
                        {% for actor in movie.actors.all %}
                            {{ actor.name }}
                        {% endfor %}
                                </span>
                        </li>
                        <li style="list-style: none"><span><b>Жанр:</b>
                        {% for genre in movie.genres.all %}
                            {{ genre.name }}
                        {% endfor %}
                                    </span>
                        </li>
                        <li style="list-style: none">
                            <span><b>Премьера в мире:</b> {{ movie.world_premiere }}</span>
                        </li>
                        <li style="list-style: none">
                            <span><b>Бюджет:</b> ${{ movie.budget }}</span></li>
                        <li style="list-style: none">
                            <span><b>Сборы в США:</b> ${{ movie.fees_in_usa }}</span></li>
                        <li style="list-style: none">
                            <span><b>Сборы в мире:</b> ${{ movie.fess_in_world }}</span>
                        </li>
                        <li style="list-style: none">
                            <a href="#"><b>Рейтинг:</b>
                                <span class="fa fa-star" aria-hidden="true"
                                      style="outline: none; cursor: inherit;"></span>
                                <span class="fa fa-star" aria-hidden="true"
                                      style="outline: none; cursor: inherit;"></span>
                                <span class="fa fa-star" aria-hidden="true"
                                      style="outline: none; cursor: inherit;"></span>
                                <span class="fa fa-star" aria-hidden="true"
                                      style="outline: none; cursor: inherit;"></span>
                                <span class="fa fa-star" aria-hidden="true"
                                      style="outline: none; cursor: inherit;"></span>
                                <span class="editContent"
                                      style="outline: none; cursor: inherit;">5.0</span>
                            </a>
                        </li>
                        <div class="share-desc">
                            <div class="share">
                                <h4 class="editContent"
                                    style="outline: none; cursor: inherit;">
                                    Share:</h4>
                                <ul class="w3layouts_social_list list-unstyled">
                                    <li>
                                        <a href="#" class="w3pvt_facebook editContent"
                                           style="outline: none; cursor: inherit;">
                                                <span class="fa fa-facebook-f"
                                                      style="outline: none; cursor: inherit;"></span>
                                        </a>
                                    </li>
                                    <li>
                                        <a href="#" class="w3pvt_twitter editContent"
                                           style="outline: none; cursor: inherit;">
                                                <span class="fa fa-twitter"
                                                      style="outline: none; cursor: inherit;"></span>
                                        </a>
                                    </li>
                                    <li>
                                        <a href="#" class="w3pvt_dribble editContent"
                                           style="outline: none; cursor: inherit;">
                                                <span class="fa fa-dribbble"
                                                      style="outline: none; cursor: inherit;"></span>
                                        </a>
                                    </li>
                                </ul>
                            </div>
                            <div class="clearfix"></div>
                        </div>
                </div>
            </div>
            <div class="row sub-para-w3layouts mt-5">

                <h3 class="shop-sing editContent">
                    О фильме {{ movie.title }}
                </h3>
                <p>
                    {% for image in movie.movieshots_set.all %}
                    <img src="{{ image.image.url }}" class="img-movie-shots "
                         alt="{{ image.description }}">
                    {% endfor %}
                </p>
                <p class="editContent" style="outline: none; cursor: inherit;">
                    {{ movie.description }}
                </p>
                <p class="mt-3 italic-blue editContent"
                   style="outline: none; cursor: inherit;">
                    <iframe width="560" height="315"
                            src="https://www.youtube.com/embed/A36LahZNUiE?controls=0"
                            frameborder="0" allow="accelerometer; autoplay;
                                                                           encrypted-media; gyroscope; picture-in-picture"
                            allowfullscreen>

                    </iframe>
                </p>ч
            </div>
            <hr>
            <div class="row">
                <div class="single-form-left">
                    <!-- contact form grid -->
                    <div class="contact-single">
                        <h3 class="editContent" style="outline: none; cursor: inherit;">
                                    <span class="sub-tittle editContent"
                                          style="outline: none; cursor: inherit;">02</span>Оставить
                            отзыв</h3>
                        <form action="{% url 'add_review' movie.id %}" method="post" class="mt-4" id="formReview">
                            {% csrf_token %}
                            <input type="hidden" name="parent" id="contactparent" value="">
                            <div class="form-group editContent">
                                <label for="contactcomment" class="editContent">
                                    Ваш комментарий *
                                </label>
                                <textarea class="form-control border" rows="5" name="text"
                                          id="contactcomment" required=""></textarea>
                            </div>
                            <div class="d-sm-flex">
                                <div class="col-sm-6 form-group p-0 editContent">
                                    <label for="contactusername" class="editContent">
                                        Имя *
                                    </label>
                                    <input type="text" class="form-control border" name="name"
                                           id="contactusername" required="">
                                </div>
                                <div class="col-sm-6 form-group ml-sm-3 editContent">
                                    <label for="contactemail" class="editContent">
                                        Email *
                                    </label>
                                    <input type="email" class="form-control border" name="email"
                                           id="contactemail" required="">
                                </div>
                            </div>
                            <button type="submit"
                                    class="mt-3 btn btn-success btn-block py-3">
                                Отправить
                            </button>
                        </form>
                    </div>
                    <!-- //contact form grid ends here -->
                </div>
            </div>
            {% for review in movie.reviews_set.all %}
            <div class="media py-5">
                <img src="bundles/images/te2.jpg" class="mr-3 img-fluid" alt="image">
                <div class="media-body mt-4">
                    <h5 class="mt-0 editContent">
                        {{ review.name }}
                    </h5>
                    <p class="mt-2 editContent">
                        {{ review.text }}
                    </p>
                    <a href="#formReview" onclick="addReview('{{ review.name }}', '{{ review.id }}')">Ответить</a>
                    {#                    <div class="media mt-5 editContent"#}
                    {#                         style="outline: none; cursor: inherit;">#}
                    {#                        <a class="pr-3" href="#">#}
                    {#                            <img src="bundles/images/te1.jpg" class="img-fluid " alt="image">#}
                    {#                        </a>#}
                    {#                        <div class="media-body">#}
                    {#                            <h5 class="mt-0 editContent"#}
                    {#                                style="outline: none; cursor: inherit;">Leia Organa</h5>#}
                    {#                            <p class="mt-2 editContent"#}
                    {#                               style="outline: none; cursor: inherit;"> Cras sit amet#}
                    {#                                nibh libero, in gravida nulla. Nulla vel metus#}
                    {#                                scelerisque ante sollicitudin. Cras purus odio,#}
                    {#                                vestibulum in vulputate at, tempus viverra turpis. Fusce#}
                    {#                                condimentum nunc ac nisi vulputate fringilla..</p>#}
                    {#                        </div>#}
                    {#                    </div>#}
                </div>
            </div>
        {% endfor %}
    </div>
    </div>
    <script>
        function addReview(name, id) {
            document.getElementById("contactparent").value = id;
            document.getElementById("contactcomment").innerText = `${name}`, 
    }
    </script>
{% endblock movie %}
```
##### 5. Experience #####
Completed an internship at GoshaTurbaTech, developed software com port.
##### 6. Education #####
Specialized secondary information technology software, English courses A2.
##### 7. English language #####
A2 level.
