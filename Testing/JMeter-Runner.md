## VM 
Para correr JMeter vamos a crear una VM temporal en Azure para evitar el impacto de la velocidad de internet de la casa del tester.

- nombre: temp-vm-jmeter-runner
- resource group: jz-rg-com
- zona: East US
- os: Windows 10 Pro
- usr: tester
- pwd: Tester123456
- NO USAR Dev/Test profile, usar PRODUCTION dado que la maquina es estable.

## Usar Remote Desktop Connection

Usando este archivo:
[temp-vm-jmeter-runner.rdp](/.attachments/temp-vm-jmeter-runner-d79a9abd-6d54-4d25-953d-280e1c9f21cf.rdp)

O conectando por Remote Desktop a
`IP: 23.96.119.164:3389`

