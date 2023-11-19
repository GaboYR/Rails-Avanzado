# Rails-Avanzado
Después de modificar el archivo **movie.rb** ubicado en **myrottenpotatoes/app/models/movie.rb** y ejecutar en consola la intrucción **rails console**
ejecutamos cada comando dado y veremos nuestros resultados y los compararemos con los del ejemplo.

![](https://github.com/GaboYR/Rails-Avanzado/blob/main/images/cap1.png)

Veamos las instrucciones
1. **m = Movie.new(:title => '', :rating => 'RG', :release_date => '1929-01-01')** : esto simplemente crea una nueva movie.
2. **m.valid?  # => false** : arroja falso debido a que la clase Movie tiene una funcion llamada
```ruby
def released_1930_or_later
        errors.add(:release_date, 'must be 1930 or later') if
        release_date && release_date < Date.parse('1 Jan 1930')
    end
```
esta funcion agrega al dato errors **'must be 1930 or later'** si se cumple que **Date.parse** detecta que la fecha es anterior al 1 de enero de 1930. 
3. **m.errors[:title] # => ["can't be blank"]** : esto se debe a que esta linea
```ruby
validates :title, :presence => true
```
indica que debe haber un título, para omitir el error podemos cambiarlo a **presence=>false**

4. **m.errors[:rating] # => [] - validation skipped for grandfathered movies** : en este caso no hay error, el arreglo esta vacío.
   
5. **m.errors[:release_date] # => ["must be 1930 or later"]** : se menciono que en la funcion **released_1930_or_later** se agrega un error, aquí muestra un mensaje de error.
   
6. **m.errors.full_messages # => ["Title can't be blank", "Release date must be 1930 or later"]**: solamente pedimos a la consola que nos muestre todos los errores de golpe.
   
## Explicar el siguiente codigo: 
```ruby
class MoviesController < ApplicationController
  def new
    @movie = Movie.new
  end 
  def create
    if (@movie = Movie.create(movie_params))
      redirect_to movies_path, :notice => "#{@movie.title} created."
    else
      flash[:alert] = "Movie #{@movie.title} could not be created: " +
        @movie.errors.full_messages.join(",")
      render 'new'
    end
  end
  def edit
    @movie = Movie.find params[:id]
  end
  def update
    @movie = Movie.find params[:id]
    if (@movie.update_attributes(movie_params))
      redirect_to movie_path(@movie), :notice => "#{@movie.title} updated."
    else
      flash[:alert] = "#{@movie.title} could not be updated: " +
        @movie.errors.full_messages.join(",")
      render 'edit'
    end
  end
  def destroy
    @movie = Movie.find(params[:id])
    @movie.destroy
    redirect_to movies_path, :notice => "#{@movie.title} deleted."
  end
  private
  def movie_params
    params.require(:movie)
    params[:movie].permit(:title,:rating,:release_date)
  end
end
```
Lo que vemos en el codigo es muy similar a las operaciones CRUD para una base de datos, ahora definiremos cada funcion:

1. new: es la funcion constructor de nuestra clase MoviesController
2. create: funcion que toma ciertos parametros y crea una nueva movie, tiene una linea de alerta en caso no se pueda crear la movie,con render nos manda a la funcion new.
3. edit: funcion que nos deja editar cierta movie, usa una funcion find para buscar el id de la movie.
4. update: funcion que actualiza nuestros datos, tiene una linea de alerta en caso haya algun problema con los datos, nos envia con render a la funcion edit.
5. destroy: funcion que permite eliminar una movie, usa la funcion find para buscarla por el id y eliminar, muestra un mensaje de confirmacion de borrado.
6. movie_params: funcion privada para que no haya modificacion directa, aplicacion del concepto de encapsulacion, dentro de esta tenemos un require y permit, los cuales permiten asegurar
   que solamente se permitan los parametros utilizados.

Despues añadimos en la clase **movie** lo siguiente:
```ruby
class Movie < ActiveRecord::Base
    before_save :capitalize_title
    def capitalize_title
        self.title = self.title.split(/\s+/).map(&:downcase).
        map(&:capitalize).join(' ')
    end
end
```
Y agregamos 
```ruby
m = Movie.create!(:title => 'STAR  wars', :release_date => '27-5-1977', :rating => 'PG')
```
Ahora verificamos que se haya corregido el nombre de la película:
![](https://github.com/GaboYR/Rails-Avanzado/blob/main/images/cap2.png)

con lo cual, ya estaría corregido.

## SSO y autenticación a través de terceros

1. Ejecutamos **rails generate model Moviegoer name:string provider:string uid:string** seguido de **rake db:migrate**.
![](https://github.com/GaboYR/Rails-Avanzado/blob/main/images/cap3.png)
eso genera un archivo ruby con la fecha actual(mostrado en la imagen)

2. Modificacion de los archivos moviegoer y routes.

![](https://github.com/GaboYR/Rails-Avanzado/blob/main/images/cap4.png)

Creacion de session_controllers y omniauth

![](https://github.com/GaboYR/Rails-Avanzado/blob/main/images/cap5.png)
