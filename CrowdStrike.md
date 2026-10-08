## CrowdStrike

Incident:

The CrowdStrike incident occurred on Friday, July 19, 2024, when the cybersecurity service provider experienced a significant issue with its Falcon product.

CrowdStrike released an online software update that caused many Windows machines to fail to boot correctly, resulting in a "blue screen" error. Resolving the issue required IT professionals to boot each affected machine in Safe Mode and manually delete a specific Falcon software file       ( C-00000291*.sys) to restore proper functionality.

Although the root cause was quickly identified, the remediation process was labor-intensive.

Causes:

Falcon, the endpoint detection and response agent of CrowdStrike operates at the kernel level on individual computers to detect threats, its client software receives periodic patches to enable it to counter new threats.

The post-incident investigation identified errors that led to the release of a flawed update to the "CrowdStrike sensor detection engine."

The main causes were:
    Channel files were validated using Regex patterns with wildcards and loaded into an array, rather than using a dedicated parser.

    In the C programming language, array lengths must be checked separately, however, the length was not verified prior to access.

    During unit testing, regression tests were not performed to verify compatibility with the previus format.
    
    In the manual tests, only valid data was tested.

    The channel files did not contain a version number field that was verified.
    
    There were no staggered updates, instead, the update was distributed to all clients simultaneously. Not even critical infrastructure received special treatment.

    It runs as a driver in Ring 0 to gain elevated privileges within the operating system. However, a failure in this area causes a Blue Screen, detaining the operating system.

Impact:
The CrowdStrike failure affected 8.5 million computers worldwide—representing a fraction of all Windows computers—but it disrupted critical systems across a wide range of companies.
It caused estimated losses exceeding $500 million.
It led to the cancellation of over 1,000 flights globally.
47% of Fortune 500 companies were CrowdStrike customers.
24.000 clients were affected globally.
London stock exange was affected for hours.

Solution:
para resolver el problema, los responsables de



Wikipedia Incidente de crowdstrike : https://es.wikipedia.org/wiki/Incidente_de_CrowdStrike_de_2024
VS Sistemas Cómo el Error de CrowdStrike Reveló Fallos Críticos en la Seguridad Informática : https://vs-sistemas.com/el-desastre-de-crowdstrike-analisis/
ElMundo El fallo de CrowdStrike afectó a 8,5 millones de ordenadores en todo el planeta : https://www.elmundo.es/tecnologia/innovacion/2024/07/22/669de8b6fc6c83be108b4599.html
