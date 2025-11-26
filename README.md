--1 ya hecho xd
--2 Seleccionar al director con menos películas
select d.nombre, d.apellido, count(*) as total_pelis from directores d
join peliculas p on d.id_dir = p.id_dir
group by d.id_dir, d.nombre, d.apellido
order by total_pelis asc
limit 1;

--3 Seleccionar al actor con más películas
select a.nombre, a.apellido, count(*) as cant_pelis from actores a
join peliculas_actores pa on a.id_actor=pa.id_actor
join peliculas p on pa.id_pel = p.id_pel
group by a.id_actor
order by cant_pelis desc
limit 1;


--4 Seleccionar las peliculas que duren más que el promedio
select p.titulo from peliculas p
where p.duracion>(select avg(duracion)from peliculas);

--5 Seleccionar el director que tiene más películas más viejas que el promedio de lanzamiento
select d.nombre, d.apellido, count(*) as cant_pelis from directores d
join peliculas p on d.id_dir = p.id_dir
where p.anio_lanzamiento<(select avg(anio_lanzamiento)from peliculas)
group by d.id_dir
order by cant_pelis DESC
limit 1;


--6 Mostrar las películas dirigidas por el director más joven
select distinct p.titulo from peliculas p 
join directores d on p.id_dir = d.id_dir
where d.anio_nac=(select max(anio_nac)from directores);

--7 Seleccionar todos los directores que hayan trabajado con Samuel Jackson
select distinct d.nombre, d.apellido from directores d
join peliculas p on d.id_dir = p.id_dir
join peliculas_actores pa on p.id_pel = pa.id_pel
join actores a on pa.id_actor = a.id_actor
where (a.nombre = 'Samuel' and a.apellido='jackson');


--8 Seleccionar todos los directores que no hayan trabajado con Darín
select distinct d.nombre, d.apellido from directores d
where d.id_dir not in (
    select distinct p.id_dir
    from peliculas p
    join peliculas_actores pa on p.id_pel = pa.id_pel
    join actores a on pa.id_actor = a.id_actor
    where a.apellido = 'darin'
);

--9 Seleccionar todos los datos de cada película, y su cantidad total de actores como “cant_actores”, de las películas que tengan más de 2 actores
select p.titulo, p.id_pel, p.duracion, p.edad_minima, p.anio_lanzamiento, p.id_dir,
count(*) as cant_actores from peliculas p
join peliculas_actores pa on p.id_pel = pa.id_pel
join actores a on pa.id_actor = a.id_actor
group by p.id_pel
HAVING(COUNT(*)>2);

--10 Seleccionar las películas que fueron hechas por cualquier director que no sea del país con más directores
select p.titulo from peliculas p
join directores d on p.id_dir = d.id_dir
where d.nacionalidad !=(
    select d.nacionalidad from directores d
    group by d.nacionalidad
    order by count(*) DESC
    limit 1
    );

--11 Seleccionar los directores que hayan dirigido 3 o más películas
select d.nombre, d.apellido from directores d
join peliculas p on d.id_dir = p.id_dir
group by d.id_dir
having (count(*)>=3)
order by count(*) desc;

--12 Asignar la película más larga al director estadounidense más viejo
update peliculas
set id_dir=(
    select id_dir
    from directores
    where nacionalidad='usa'
    order by anio_nac asc
    limit 1
)
where id_pel=(
    select id_pel
    from peliculas
    order by duracion desc
    limit 1
);

--13 Poner de nombre “José” a todos los actores que hayan trabajado en una película más vieja que 2010 o más larga que el promedio
update actores
set nombre='josé'
where id_actor in(
    select distinct a.id_actor from actores a
    join peliculas_actores pa on a.id_actor = pa.id_actor
    join peliculas p on pa.id_pel = p.id_pel
    where (p.anio_lanzamiento<2010) OR 
    duracion>(select avg(duracion) from peliculas)
);

delete from peliculas_actores where id_pel in (select id_pel from peliculas where duracion<(select avg(duracion)from peliculas));

delete from peliculas where id_pel in (select id_pel from peliculas where duracion<(select avg(duracion)from peliculas));
