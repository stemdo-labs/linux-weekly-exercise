# EJERCICIO SEMANAL

## Enunciado:

Una forma de determinar la validez de una cuenta de usuario en linux es verificar si es propietaria de archivos y si es así, ver cuál fue la última fecha de modificación.
Realiza un script capaz de obtener la siguiente información sobre las cuentas:

        ◦ No tengan archivos.
        ◦ Los archivos hace 6 meses que no se modifican.
        ◦ Los archivos hace 1 año que no se modifican.
        ◦ Obtener las cuentas que cumplan el punto 1 o el 3.

- Cada apartado debe estar definido como una función (cuatro en total) dentro del script. 
- A la hora de llamar el script, se le introducirá como parámetro el nombre de la función que se desea ejecutar.
- El output por pantalla debe mostrarse separado por comas, como en el ejemplo.

(Opcional)

- El propio script detectará si no se le está pasando un parámetro válido y te devolverá la lista de parámetros válidos.
- El script necesitará permisos de sudo para ejecutarse. En caso de no dárselos en el momento de ejecutarlo, que sea el propio script el que se los otorgue.

## Solución

Para empezar crearemos un script `.sh` el cual contendra todo el siguiente código:

### usuarioScript.sh

```script
!/bin/bash

# Comprobacion de sudo

if [ "$EUID" -ne 0 ]; then
        echo "Permisos de sudo no encontrados, ejecutando de nuevo con ellos."
        exec sudo bash "$0" "$@"
fi

# Funcion cuentas sin archivos

sinarchivos() {
        echo "Cuentas de usuarios sin archivos"
        for user in $(cut -d: -f1 /etc/passwd); do
                if ! find / -user $user -xdev > /dev/null 2>&1; then
                        echo -n "$user, "
                fi
        done
        echo
}

# Funcion cuentas con archivos que no se han modificado en 6 meses

sinmodificar_6meses(){
        echo "Cuentas de usuarios sin modificar 6 meses"
        for user in $(cut -d: -f1 /etc/passwd); do
                if find / -user $user -xdev -mtime +180 -print | grep -q .;then
                        echo -n "$user, "
                fi
        done
        echo
}

# Funcion cuentas con archivos que no se han modificado en 1 año

sinmodificar_1anio(){
        echo "Cuentas de usuarios sin modificar 1 año"
        for user in $(cut -d: -f1 /etc/passwd); do
                if find / -user $user -xdev -mtime +360 -print | grep -q .;then
                        echo -n "$user, "
                fi
        done
        echo
}

# Funcion de cuentas que no tienen archivos o no se han modificado en un año

sinarchivos_sinmodificar1anio(){
        for user in $(cut -d: -f1 /etc/passwd);do
                if ! find / -user $user -xdev > /dev/null 2>/dev/null || find / -user $user -xdev -mtime +360 -print | grep -q .;then
                        echo -n "$user, "
                fi
        done
        echo
}

# Ejecuta la función

case "$1" in 
        "sinarchivos")
                sinarchivos
        ;;
        "sinmodificar_6meses")
                sinmodificar_6meses
        ;;
        "sinmodificar_1anio")
                sinmodificar_1anio
        ;;
        "sinarchivos_sinmodificar1anio")
                sinarchivos_sinmodificar1anio
        ;;
        *)
                echo "Parametro invalido. Los parametros validos son: sinarchivos, sinmodificar_6meses, sinmodificar_1anio, sinarchivos_sinmodificar1anio"
                exit 1
        ;;
esac
```
Para ejecutar el script en la terminal prondremos el siguiente comando:

```bas
./scriptUsuarios.sh <Nombre_funcion>
```

### Capturas de pantalla

#### Parametro invalido

[![Captura-de-pantalla-2024-11-12-145207.png](https://i.postimg.cc/wvggSwnb/Captura-de-pantalla-2024-11-12-145207.png)](https://postimg.cc/QB427J8p)









