---
layout: default
title: "Ejemplo con markdownify"
---

## Introducción

Esto es una introducción breve que siempre se muestra.

<button onclick="document.getElementById('contenido-completo').style.display='block'; this.style.display='none';">
    Leer más
</button>

<div id="contenido-completo" style="display: none;">
    {{ """
    ## Contenido Completo

    ### Este es un título dentro del contenido oculto

    - Punto 1
    - Punto 2
    - Punto 3
    """ | markdownify }}
</div>
