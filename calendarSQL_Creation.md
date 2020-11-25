~~~ sql
CREATE DATABASE calendar OWNER = alumno ;

CREATE SCHEMA calendar;

CREATE TABLE IF NOT EXISTS calendar.comparte
(
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
PRIMARY KEY (dni_contacto, dni_usuario)
);

CREATE TABLE IF NOT EXISTS calendar.usuarios
(
DNi_usuario  VARCHAR(10) PRIMARY KEY,
nombre_completo VARCHAR(30) NOT NULL,
email VARCHAR(40) UNIQUE,
contraseña VARCHAR(20) UNIQUE NOT NULL,
direccion VARCHAR(50),
poblacion VARCHAR(20),
portal VARCHAR(5),
codigo_postal INTEGER,
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
titulo VARCHAR(30),
descripcion VARCHAR(300),
hora_alarma TIME,
PRIMARY KEY (evento, fecha)
);

CREATE TABLE IF NOT EXISTS calendar.tareas
(
evento INTEGER,
fecha DATE,
calendario INTEGER,
titulo VARCHAR(30),
descripcion VARCHAR(300),
hora_inicio TIME,
PRIMARY KEY (evento, fecha)
);

CREATE TABLE IF NOT EXISTS calendar.festivos
(
evento INTEGER,
fecha DATE,
calendario INTEGER,
festividad VARCHAR(40),
ambito VARCHAR(20),
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
titulo VARCHAR(30),
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
FOREIGN KEY (evento) REFERENCES calendar.eventos (evento)
ON DELETE CASCADE ON UPDATE CASCADE;

ALTER TABLE calendar.recordatorio ADD CONSTRAINT clave_ajena2_recordatorios
FOREIGN KEY (fecha) REFERENCES calendar.eventos (fecha)
ON DELETE CASCADE ON UPDATE CASCADE;

ALTER TABLE calendar.recordatorio ADD CONSTRAINT clave_ajena3_recordatorios
FOREIGN KEY (calendario) REFERENCES calendar.calendarios (calendario)
ON DELETE CASCADE ON UPDATE CASCADE;

ALTER TABLE calendar.tareas ADD CONSTRAINT clave_ajena1_tareas
FOREIGN KEY (evento) REFERENCES calendar.eventos (evento)
ON DELETE CASCADE ON UPDATE CASCADE;

ALTER TABLE calendar.tareas ADD CONSTRAINT clave_ajena2_tareas
FOREIGN KEY (fecha) REFERENCES calendar.eventos (fecha)
ON DELETE CASCADE ON UPDATE CASCADE;

ALTER TABLE calendar.tareas ADD CONSTRAINT clave_ajena3_tareas
FOREIGN KEY (calendario) REFERENCES calendar.calendarios (calendario)
ON DELETE CASCADE ON UPDATE CASCADE;

ALTER TABLE calendar.festivos ADD CONSTRAINT clave_ajena1_festivos
FOREIGN KEY (evento) REFERENCES calendar.eventos (evento)
ON DELETE CASCADE ON UPDATE CASCADE;

ALTER TABLE calendar.festivos ADD CONSTRAINT clave_ajena2_festivos
FOREIGN KEY (fecha) REFERENCES calendar.eventos (fecha)
ON DELETE CASCADE ON UPDATE CASCADE;

ALTER TABLE calendar.festivos ADD CONSTRAINT clave_ajena3_festivos
FOREIGN KEY (calendario) REFERENCES calendar.calendarios (calendario)
ON DELETE CASCADE ON UPDATE CASCADE;

ALTER TABLE calendar.cumpleaños ADD CONSTRAINT clave_ajena1_cumpleaños
FOREIGN KEY (evento) REFERENCES calendar.eventos (evento)
ON DELETE CASCADE ON UPDATE CASCADE;

ALTER TABLE calendar.cumpleaños ADD CONSTRAINT clave_ajena2_cumpleaños
FOREIGN KEY (fecha) REFERENCES calendar.eventos (fecha)
ON DELETE CASCADE ON UPDATE CASCADE;

ALTER TABLE calendar.cumpleaños ADD CONSTRAINT clave_ajena3_cumpleaños
FOREIGN KEY (calendario) REFERENCES calendar.calendarios (calendario)
ON DELETE CASCADE ON UPDATE CASCADE;

ALTER TABLE calendar.cumpleaños ADD CONSTRAINT clave_ajena4_cumpleaños
FOREIGN KEY (dni_usuario) REFERENCES calendar.usuarios (dni_usuario)
ON DELETE CASCADE ON UPDATE CASCADE;

ALTER TABLE calendar.eventogenerico ADD CONSTRAINT clave_ajena1_eventogenerico
FOREIGN KEY (evento) REFERENCES calendar.eventos (evento)
ON DELETE CASCADE ON UPDATE CASCADE;

ALTER TABLE calendar.eventogenerico ADD CONSTRAINT clave_ajena2_eventogenerico
FOREIGN KEY (fecha) REFERENCES calendar.eventos (fecha)
ON DELETE CASCADE ON UPDATE CASCADE;

ALTER TABLE calendar.eventogenerico ADD CONSTRAINT clave_ajena3_eventogenerico
FOREIGN KEY (calendario) REFERENCES calendar.calendarios (calendario)
ON DELETE CASCADE ON UPDATE CASCADE;

ALTER TABLE calendar.seetiquetacon ADD CONSTRAINT clave_ajena_1_seetiquetacon
FOREIGN KEY (etiqueta) REFERENCES calendar.etiqueta (etiqueta)
ON DELETE CASCADE ON UPDATE CASCADE;

ALTER TABLE calendar.seetiquetacon ADD CONSTRAINT clave_ajena2_seetiquetacon
FOREIGN KEY (evento) REFERENCES calendar.eventogenerico (evento)
ON DELETE CASCADE ON UPDATE CASCADE;

ALTER TABLE calendar.seetiquetacon ADD CONSTRAINT clave_ajena3_seetiquetacon
FOREIGN KEY (fecha) REFERENCES calendar.eventogenerico (fecha)
ON DELETE CASCADE ON UPDATE CASCADE;


~~~ 
