~~~ sql
CREATE DATABASE ejercicio_calendar OWNER = alumno ;

CREATE EXTENSION citext;
CREATE DOMAIN email AS citext
  CHECK ( value ~ '^[a-zA-Z0-9.!#$%&''*+/=?^_`{|}~-]+@[a-zA-Z0-9](?:[a-zA-Z0-9-]{0,61}[a-zA-Z0-9])?(?:\.[a-zA-Z0-9](?:[a-zA-Z0-9-]{0,61}[a-zA-Z0-9])?)*$' );

CREATE SCHEMA calendar;

CREATE TABLE IF NOT EXISTS calendar.comparte
(
-- ESTE USUARIO ES EL QUE VA A RECIVIR LA COMPARTICION DE LA TABLA
-- HACER UN CHECK ENTRE TABLAS PARA COMPROBAR QUE NO SE COMPARTE UN CALENDARIO CON SIGO MISMO
dni_usuario VARCHAR(10),
calendario INTEGER,
PRIMARY KEY (dni_usuario, calendario)
);

CREATE TABLE IF NOT EXISTS calendar.calendarios
(
calendario SERIAl PRIMARY KEY,
DNI_usuario VARCHAR(10),
fecha_creacion TIMESTAMP NOT NULL
);

CREATE TABLE IF NOT EXISTS calendar.guardarcontacto
(
DNI_contacto VARCHAR(10),
DNI_usuario VARCHAR(10),
CHECK(DNI_usuario != DNI_contacto),
PRIMARY KEY (dni_contacto, dni_usuario)
);

CREATE TABLE IF NOT EXISTS calendar.usuarios
(
DNi_usuario  VARCHAR(10) PRIMARY KEY,
nombre_completo VARCHAR(30) NOT NULL,
email email UNIQUE NOT NULL,
contraseña CHAR(64) NOT NULL,
direccion VARCHAR(50) NOT NULL,
poblacion VARCHAR(20)NOT NULL,
portal VARCHAR(5),
codigo_postal INTEGER NOT NULL,
fecha_nacimiento DATE NOT NULL
);

CREATE TABLE IF NOT EXISTS calendar.eventos
(
evento SERIAL,
fecha DATE,
calendario INTEGER UNIQUE,
PRIMARY KEY (evento, fecha)
);

CREATE TABLE IF NOT EXISTS calendar.recordatorio
(
evento INTEGER,
fecha DATE,
calendario INTEGER,
titulo VARCHAR(30) NOT NULL,
descripcion VARCHAR(300),
hora_alarma TIME NOT NULL,
PRIMARY KEY (evento, fecha)
);

CREATE TABLE IF NOT EXISTS calendar.tareas
(
evento INTEGER,
fecha DATE,
calendario INTEGER,
titulo VARCHAR(30) NOT NULL,
descripcion VARCHAR(300),
hora_inicio TIME,
PRIMARY KEY (evento, fecha)
);

CREATE TABLE IF NOT EXISTS calendar.festivos
(
evento INTEGER,
fecha DATE,
calendario INTEGER,
festividad VARCHAR(40) NOT NULL,
ambito VARCHAR(20) NOT NULL,
PRIMARY KEY (evento, fecha)
);

CREATE TABLE IF NOT EXISTS calendar.cumpleaños
(
evento INTEGER,
fecha DATE,
calendario INTEGER,
dni_usuario VARCHAR(30),
PRIMARY KEY (evento, fecha)
);

CREATE TABLE IF NOT EXISTS calendar.eventogenerico
(
evento INTEGER,
fecha DATE,
calendario INTEGER,
titulo VARCHAR(30) NOT NULL,
descripcion VARCHAR(300),
localizacion VARCHAR(40),
PRIMARY KEY (evento, fecha)
);

CREATE TABLE IF NOT EXISTS calendar.seetiquetacon
(
etiqueta INTEGER,
evento INTEGER,
fecha DATE,
PRIMARY KEY (etiqueta, evento, fecha)
);

CREATE TABLE IF NOT EXISTS calendar.etiqueta
(
etiqueta SERIAL PRIMARY KEY,
titulo VARCHAR(30) NOT NULL,
color CHAR(7) NOT NULL
);

ALTER TABLE calendar.calendarios ALTER COLUMN fecha_creacion SET DEFAULT now();

ALTER TABLE calendar.comparte ADD CONSTRAINT clave_ajena1_comparte
FOREIGN KEY (dni_usuario) REFERENCES calendar.usuarios (dni_usuario)
ON DELETE CASCADE ON UPDATE CASCADE;

ALTER TABLE calendar.comparte ADD CONSTRAINT clave_ajena2_comparte
FOREIGN KEY (calendario) REFERENCES calendar.calendarios (calendario)
ON DELETE CASCADE ON UPDATE CASCADE;

ALTER TABLE calendar.calendarios ADD CONSTRAINT clave_ajena_calendarios
FOREIGN KEY (dni_usuario) REFERENCES calendar.usuarios (dni_usuario)
ON DELETE CASCADE ON UPDATE CASCADE;

ALTER TABLE calendar.guardarcontacto ADD CONSTRAINT clave_ajena1_guardarcontacto
FOREIGN KEY (dni_contacto) REFERENCES calendar.usuarios (dni_usuario)
ON DELETE CASCADE ON UPDATE CASCADE;

ALTER TABLE calendar.guardarcontacto ADD CONSTRAINT clave_ajena2_guardarcontacto
FOREIGN KEY (dni_usuario) REFERENCES calendar.usuarios (dni_usuario)
ON DELETE CASCADE ON UPDATE CASCADE;

ALTER TABLE calendar.eventos ADD CONSTRAINT clave_ajena_eventos
FOREIGN KEY (calendario) REFERENCES calendar.calendarios (calendario)
ON DELETE CASCADE ON UPDATE CASCADE;

ALTER TABLE calendar.recordatorio ADD CONSTRAINT clave_ajena1_recordatorios
FOREIGN KEY (evento, fecha) REFERENCES calendar.eventos (evento, fecha)
ON DELETE CASCADE ON UPDATE CASCADE;

ALTER TABLE calendar.recordatorio ADD CONSTRAINT clave_ajena2_recordatorios
FOREIGN KEY (calendario) REFERENCES calendar.calendarios (calendario)
ON DELETE CASCADE ON UPDATE CASCADE;

ALTER TABLE calendar.tareas ADD CONSTRAINT clave_ajena1_tareas
FOREIGN KEY (evento, fecha) REFERENCES calendar.eventos (evento, fecha)
ON DELETE CASCADE ON UPDATE CASCADE;

ALTER TABLE calendar.tareas ADD CONSTRAINT clave_ajena2_tareas
FOREIGN KEY (calendario) REFERENCES calendar.calendarios (calendario)
ON DELETE CASCADE ON UPDATE CASCADE;

ALTER TABLE calendar.festivos ADD CONSTRAINT clave_ajena1_festivos
FOREIGN KEY (evento, fecha) REFERENCES calendar.eventos (evento, fecha)
ON DELETE CASCADE ON UPDATE CASCADE;

ALTER TABLE calendar.festivos ADD CONSTRAINT clave_ajena2_festivos
FOREIGN KEY (calendario) REFERENCES calendar.calendarios (calendario)
ON DELETE CASCADE ON UPDATE CASCADE;

ALTER TABLE calendar.cumpleaños ADD CONSTRAINT clave_ajena1_cumpleaños
FOREIGN KEY (evento, fecha) REFERENCES calendar.eventos (evento, fecha)
ON DELETE CASCADE ON UPDATE CASCADE;

ALTER TABLE calendar.cumpleaños ADD CONSTRAINT clave_ajena2_cumpleaños
FOREIGN KEY (calendario) REFERENCES calendar.calendarios (calendario)
ON DELETE CASCADE ON UPDATE CASCADE;

ALTER TABLE calendar.cumpleaños ADD CONSTRAINT clave_ajena3_cumpleaños
FOREIGN KEY (dni_usuario) REFERENCES calendar.usuarios (dni_usuario)
ON DELETE CASCADE ON UPDATE CASCADE;

ALTER TABLE calendar.eventogenerico ADD CONSTRAINT clave_ajena1_eventogenerico
FOREIGN KEY (evento, fecha) REFERENCES calendar.eventos (evento, fecha)
ON DELETE CASCADE ON UPDATE CASCADE;

ALTER TABLE calendar.eventogenerico ADD CONSTRAINT clave_ajena2_eventogenerico
FOREIGN KEY (calendario) REFERENCES calendar.calendarios (calendario)
ON DELETE CASCADE ON UPDATE CASCADE;

ALTER TABLE calendar.seetiquetacon ADD CONSTRAINT clave_ajena_1_seetiquetacon
FOREIGN KEY (etiqueta) REFERENCES calendar.etiqueta (etiqueta)
ON DELETE CASCADE ON UPDATE CASCADE;

ALTER TABLE calendar.seetiquetacon ADD CONSTRAINT clave_ajena2_seetiquetacon
FOREIGN KEY (evento, fecha) REFERENCES calendar.eventogenerico (evento, fecha)
ON DELETE CASCADE ON UPDATE CASCADE;

-- 4 Usuarios 

INSERT INTO calendar.usuarios(
	dni_usuario, nombre_completo, email, "contraseña",
	direccion, poblacion, portal, codigo_postal, fecha_nacimiento)
VALUES
('79336700V', 'Jose Angel Doval Fraga',
'doval@jose.doval.com', '123', 'Roibeira 48',
'betanzos', null, 15300, '1985-12-07'),
('11111111A', 'Daniel Di Pascua',
'daniel@DiPascua.com', 'abc123.', 'Calle de prueba 123',
'sada', '1A', 15300, '1988-04-11'),
('22222222J', 'Miguel Rios Cortes',
'miguel@rios.com', '123', 'calle venezuela n8',
'A coruña', 'C', 16006, '1980-01-01'),
('33333333H', 'Macos Rojo Azul',
'marcos@rojo.com', '123', 'calle alfonso Molina nº 30',
'A coruña', 'C', 16006, '1975-09-11')
;

-- Calendarios de cada Usuario (4 calendarios)

INSERT INTO calendar.calendarios(
	calendario, dni_usuario, fecha_creacion)
VALUES
(DEFAULT, '79336700V', DEFAULT),
(DEFAULT, '11111111A', DEFAULT),
(DEFAULT, '22222222J', DEFAULT),
(DEFAULT, '33333333H', DEFAULT)
;

-- Contactos Usuario 1 tiene de contacto a usuario 3, Usuario 4 tiene de contacto a usuario 1 y 2

INSERT INTO calendar.guardarcontacto(
	dni_contacto, dni_usuario)
VALUES
('79336700V', '22222222J'),
('33333333H', '79336700V'),
('33333333H', '11111111A')
;

-- Usuario 1 comparte calendario con usuario 3, Usuario 4 comparte calendario con Usuario 1

INSERT INTO calendar.comparte(
	dni_usuario, calendario)
VALUES
('22222222J', '1'),
('79336700V', '4')
;

-- Creamos los eventos del Usuario 1



~~~ 
