# PruebaADL_Modulo5

Prueba de introduccion a base de datos

1. Revisa el tipo de relación y crea el modelo correspondiente. Respeta las claves primarias, foráneas y tipos de datos.

```SQL
    Create table Peliculas(
        id serial,
        nombre varchar(255),
        anno integer,
        primary key(id)
    );
    Create table Tags(
        id serial,
        tag varchar(32),
        primary key(id)
    );
    Create table PeliculaTags(
        id serial primary key,
        pelicula_id int not null,
        tag_id int not null,
        foreign key (pelicula_id) references Peliculas (id) on delete cascade,
        foreign key (tag_id) references Tags (id) on delete cascade,
        unique (pelicula_id, tag_id)
    );
```

2. Inserta 5 películas y 5 tags; la primera película debe tener 3 tags asociados, la segunda película debe tener 2 tags asociados.

```SQL
    INSERT INTO Peliculas (nombre, anno)
    VALUES
    ('The Shawshank Redemption', 1994),
    ('The Dark Knight', 2008),
    ('Pulp Fiction', 1994),
    ('Forrest Gump', 1994),
    ('Inception', 2010); 

    INSERT INTO Tags (tag)
    VALUES
    ('Drama'),
    ('Crime'),
    ('Action'),
    ('Thriller'),
    ('Classic');

    INSERT INTO PeliculaTags (pelicula_id, tag_id)
    VALUES
    (1, 1),
    (1, 2),
    (1, 5),
    (2, 3),
    (2, 4),
    (3, 2),
    (3, 4),
    (4, 1),
    (4, 5),
    (5, 3),
    (5, 4);
```

3. Cuenta la cantidad de tags que tiene cada película. Si una película no tiene tags debe mostrar 0.

```SQL
    select p.nombre, count(pt.tag_id) from PeliculaTags pt 
    right join Peliculas p on p.id = pt.pelicula_id 
    group by pt.pelicula_id, p.nombre
    order by pt.pelicula_id asc;
```

4. Crea las tablas correspondientes respetando los nombres, tipos, claves primarias y foráneas y tipos de datos.

```SQL
    Create table Preguntas (
        id serial primary key,
        pregunta varchar(255) not null,
        respuesta_correcta varchar(255) not null
    );
    Create table Usuarios (
        id serial primary key,
        nombre varchar(255) not null,
        edad int not null
    );
    Create table Respuestas (
        id serial primary key,
        respuesta varchar(255) not null,
        usuario_id int not null,
        pregunta_id int not null,
        foreign key (usuario_id) references Usuarios(id) on delete cascade,
        foreign key (pregunta_id) references Preguntas(id) on delete cascade
    );
```

5. Agrega 5 usuarios y 5 preguntas:
    a. La primera pregunta debe estar respondida correctamente dos veces, por dos usuarios diferentes.
    b. La segunda pregunta debe estar contestada correctamente solo por un usuario.
    c. Las otras tres preguntas deben tener respuestas incorrectas.
    Contestada correctamente signica que la respuesta indicada en la tabla respuestas es exactamente igual al texto indicado en la tabla de preguntas.

```SQL
    INSERT INTO Usuarios (nombre, edad)
    VALUES
    ('Diego', 30),
    ('Ana', 25),
    ('Carlos', 28),
    ('María', 22),
    ('Luis', 35);

    INSERT INTO Preguntas (pregunta, respuesta_correcta)
    VALUES
    ('¿Cuál es la capital de Francia?', 'París'),
    ('¿Cuál es el resultado de 2 + 2?', '4'),
    ('¿Quién escribió Hamlet?', 'Shakespeare'),
    ('¿Cuál es el elemento químico del agua?', 'H2O'),
    ('¿Cuántos planetas hay en el sistema solar?', '8');

    INSERT INTO Respuestas (respuesta, usuario_id, pregunta_id)
    VALUES
    ('París', 1, 1),
    ('París', 2, 1),
    ('4', 3, 2),
    ('Miguel de Cervantes', 4, 3),
    ('Oxígeno', 5, 4),
    ('7', 1, 5);
```

6. Cuenta la cantidad de respuestas correctas totales por usuario (independiente de la pregunta).

```SQL
    select u.id, u.nombre, count(r.id) from Usuarios u
        join Respuestas r on u.id = r.usuario_id
        join Preguntas p on r.pregunta_id = p.id
        where r.respuesta = p.respuesta_correcta
        group by u.id, u.nombre, r.id
        order by r.id desc;
```

7. Por cada pregunta, en la tabla preguntas, cuenta cuántos usuarios respondieron correctamente.

```SQL
    select p.id, p.pregunta, count(case when r.respuesta = p.respuesta_correcta then 1 end) from Preguntas p 
    left join respuestas r on p.id = r.pregunta_id
    group by p.id, p.pregunta
    order by p.id asc;
```

8. Implementa un borrado en cascada de las respuestas al borrar un usuario. Prueba la implementación borrando el primer usuario.

```SQL
    delete from Usuarios where id = 1;
    select * from Respuestas where usuario_id=1;
```

9. Crea una restricción que impida insertar usuarios menores de 18 años en la base de datos.

```SQL
    alter table Usuarios add constraint chk_edad check (edad >= 18);
```

10. Altera la tabla existente de usuarios agregando el campo email. Debe tener la restricción de ser único.

```SQL
    alter table Usuarios add column email varchar(255);
    alter table Usuarios add constraint unq_email unique(email);
```
