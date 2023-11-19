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
