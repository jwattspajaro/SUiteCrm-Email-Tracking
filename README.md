SuiteCRM Email Tracking

Este módulo permite rastrear cuándo un destinatario abre un correo enviado desde SuiteCRM 8.8, insertando un pixel de seguimiento en los correos electrónicos enviados.

 Características

Agrega un tracking pixel en cada correo enviado desde SuiteCRM.

Registra la apertura del correo en la base de datos.

Permite monitorear el número de veces que un correo es abierto.

Instalación

Modificar Save.php

Ubicación del archivo:📂 public/legacy/modules/Emails/Save.php

Añadir el siguiente código en la parte donde se define el cuerpo del correo:

// Agregar Tracking Pixel al contenido del correo
$tracking_url = 'https://tu-dominio.com/suitecrm_track.php?email_id=' . $focus->id;
$tracking_pixel = '<img src="' . $tracking_url . '" width="1" height="1" style="display:none;">';

// Insertar el pixel en el contenido del correo
$focus->description_html .= $tracking_pixel;

// Guardar cambios en la base de datos
$focus->save();

Crear el archivo suitecrm_track.php

Ubicación recomendada:📂 /public_html/suitecrm/ (para hosting compartido)📂 /var/www/html/suitecrm/public/ (para servidores dedicados)

📄 Código para suitecrm_track.php:

<?php
// Conexión a la base de datos de SuiteCRM
require_once 'config.php';
require_once 'include/database/DBManager.php';

global $db;
$email_id = $_GET['email_id'] ?? '';

if ($email_id) {
    $query = "UPDATE emails SET open_count = open_count + 1 WHERE id = '" . $db->quote($email_id) . "'";
    $db->query($query);
}

// Devolver una imagen transparente para no afectar la visualización
theader('Content-Type: image/gif');
echo base64_decode('R0lGODlhAQABAAAAACw=');
?>

Configuración de Permisos

Después de subir suitecrm_track.php, asigna los permisos correctos:

chmod 644 /var/www/html/suitecrm/public/suitecrm_track.php
chown www-data:www-data /var/www/html/suitecrm/public/suitecrm_track.php

Pruebas y Verificación

Envía un correo desde SuiteCRM.2️⃣ Abre el correo en otro navegador/dispositivo.3️⃣ Verifica la base de datos ejecutando:

SELECT id, open_count FROM emails WHERE id = 'ID_DEL_CORREO';

Si open_count aumenta, ¡el tracking funciona correctamente! 🎯

Solución de Problemas

El pixel no se carga → Asegúrate de que suitecrm_track.php sea accesible desde la web.

No aumenta el open_count → Revisa que el email_id se esté pasando correctamente en la URL.

Personalización: Puedes expandir este sistema para generar reportes de aperturas dentro de SuiteCRM.

¿Dudas o mejoras? Contáctame para soporte técnico. 🚀

