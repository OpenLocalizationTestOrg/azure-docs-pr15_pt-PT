## <a name="defining-a-backup-policy"></a>Definir uma política de cópia de segurança

Uma política de cópia de segurança define uma matriz de quando os instantâneos dados sejam tomados e quanto tempo são mantidos esses instantâneos. Quando definir uma política para cópias de segurança uma VM, pode acionar uma tarefa de cópia de *uma vez por dia*. Quando cria uma nova política, é aplicado ao Cofre de palavras. Interface da política de cópia de segurança de tem o seguinte aspeto:

![Política de cópia de segurança](./media/backup-create-policy-for-vms/backup-policy.png)

Para criar uma política de:

1. Introduza um nome para o **nome da política**.

2. Podem ser tomados instantâneos dos seus dados em intervalos diária ou semanal. Utilize o menu pendente **Frequência de cópia de segurança** para escolher se instantâneos dados sejam tomados diária ou semanal.

    - Se optar por um intervalo de diário, utilize o controlo realçado para selecionar a hora do dia do limite da. Para alterar a hora, anule a hora e selecione a nova hora.

    ![Política de cópia de segurança diária](./media/backup-create-policy-for-vms/backup-policy-daily.png) <br/>

    - Se optar por um intervalo semanal, utilize os controlos realçados para selecionar o dia da semana (s) e a hora do dia para tirar o instantâneo. No menu do dia, selecione um ou vários dias. No menu de hora, selecione uma hora. Para alterar a hora, anule a hora selecionada e selecione a nova hora.

    ![Política de cópia de segurança semanal](./media/backup-create-policy-for-vms/backup-policy-weekly.png)

3. Por predefinição, todas as opções de **Intervalo de retenção** são selecionadas. Desmarque qualquer limite de retenção do intervalo que não pretende utilizar. Em seguida, especifique o interval(s) para utilizar.

    Mensal e anual intervalos de retenção permitem-lhe especificar os instantâneos com base num incremento diário ou semanal.

    >[AZURE.NOTE] Quando proteger uma VM, uma tarefa de cópia de segurança é executado uma vez por dia. O tempo quando executa a cópia de segurança é o mesmo para cada intervalo de retenção.

4. Depois de definir todas as opções para a política, na parte superior da pá, clique em **Guardar**.

    A nova política é aplicada imediatamente à cofre.
