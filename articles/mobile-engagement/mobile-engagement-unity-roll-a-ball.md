<properties
    pageTitle="Imagens da unidade um tutorial bola"
    description="Passos para criar a unidade clássica filmar um jogo bola que é uma pré-necessários para todos os tutoriais de unidade de Cativação Mobile"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/19/2016"
    ms.author="piyushjo" />

#<a id="unity-roll-a-ball"></a>Criar um filmar um jogo bola

Neste tutorial explica os passos principais numa [unidade recuperar um tutorial bola](http://unity3d.com/learn/tutorials/projects/roll-ball-tutorial)de forma ligeiramente modificadas. Este jogo de amostra é constituído por um objeto de esférica 'Leitor' que é controlado pelo utilizador app e sobre o objetivo do jogo é ' recolher ' objetos collectible por choque com o objeto de leitor com estes objetos collectible. Este comando assume básica familiaridade com ambiente do editor de unidade. Se ocorrerem problemas, em seguida, deve referir-se para o tutorial completo. 

### <a name="setting-up-the-game"></a>Configurar o jogo
Os passos abaixo são a partir do [tutorial de unidade](https://unity3d.com/learn/tutorials/projects/roll-a-ball/set-up?playlist=17141)

1. Abrir o **Editor de unidade** e clique em **Novo**. 
    
    ![][51] 
    
2. Forneça um **nome do projeto** & **localização**, selecione **3D** e clique em **Criar projeto**.
    
    ![][52]

3. Guardar a cena predefinido que acabou de criar como parte do novo projeto tal como acontece com o nome **MiniGame** dentro de uma nova ** \_cenas** pasta em pasta de **elementos** :
    
    ![][53]

4. Criar um **objeto 3D -> plano** como o campo de jogo e mude o nome deste objecto plano como **terra**

    ![][1]

5. Repor o componente de transformação para este objecto de **raiz** para que seja na origem. 

    ![][3]

6. Desmarque **Mostrar grelha** a partir do **Gizmos menu** para o objeto de **raiz** .

    ![][4]

7. Atualizar o componente de **escala** para o objeto de **raiz** ser [X = 2, Y = 1, Z = 2]. 

    ![][5]

8. Adicionar um novo **objeto 3D -> domínio** ao projecto e mudar o nome do objeto este domínio como **leitor**. 

    ![][6]

9. Selecione o objeto de **leitor** e clique em **Repor transformar** semelhante ao objeto plano. 

10. Actualização **transformar -> posição -> coordenada Y** componente para o leitor de Y como 0,5.  

    ![][7]

11. Crie uma nova pasta denominada **materiais** no projeto onde irá criamos o material a cor do leitor. 

12. Crie um novo **Material** denominado **fundo** nesta pasta. 

    ![][8]

13. Atualize a cor do material ao atualizar a propriedade **Albedo** do mesmo. Pode selecionar os valores RGB de [0,32,64]. 

    ![][9]

14. Arraste esta material para a vista de cena para aplicar cor para o objeto de **raiz** . 

    ![][10]

17. Por fim atualizar o **transformar -> rotação -> Y** para 60 no objeto Light direccionais para maior clareza. 

    ![][12]

### <a name="moving-the-player"></a>Mover o leitor
Os passos abaixo são a partir do [tutorial de unidade](https://unity3d.com/learn/tutorials/projects/roll-a-ball/moving-the-player?playlist=17141)

1. Adicione um componente de **RigidBody** ao objeto **Player** . 

    ![][13]

2. Crie uma nova pasta denominada **Scripts** no projeto. 

3. Clique em **Adicionar componente -> Novo Script -> c# Script**. O nome **PlayerController**e clique em **criar e adicionar**. Isto irá criar e anexar um script para o objeto de leitor.  

    ![][14]

5. Deslocar-se este script na pasta de **Scripts** no projeto. 

6. Abra o script para edição no seu editor de scripts Favoritos, atualize o código de script com o seguinte código e guardá-lo. 

        using UnityEngine;
        using System.Collections;
        
        public class PlayerController : MonoBehaviour 
        {
            public float speed;
            private Rigidbody rb;
            void Start ()
            {
                rb = GetComponent<Rigidbody>();
            }
            void FixedUpdate ()
            {
                float moveHorizontal = Input.GetAxis ("Horizontal");
                float moveVertical = Input.GetAxis ("Vertical");
                Vector3 movement = new Vector3 (moveHorizontal, 0.0f, moveVertical);
                rb.AddForce (movement * speed);
            }
        }
    
8. Repare que o script acima utiliza uma propriedade de **velocidade** . No editor de unidade, atualize a propriedade de velocidade para 10.  

    ![][15]

9. Acertos **Reproduzir** no Editor de unidade. Agora deverá conseguir controlar a bola utilizando o teclado e deve rodar e deslocar-se. 

### <a name="moving-the-camera"></a>Mover a câmara
Os passos abaixo são a partir do [tutorial de unidade](https://unity3d.com/learn/tutorials/projects/roll-a-ball/moving-the-camera?playlist=17141) e irão associam a **Câmara principal** para o objeto do **leitor** . 

1. Atualizar a **Transform.Position** para ser X = 0, Y = 10.5, Z =-10.  
2. Atualizar a **Transform.Rotation** para ser X = 45, Y = 0, Z = 0.  

    ![][16]

2. Adicionar um novo script denominado **CameraController** para o **MainCamera** e movê-la na pasta de Scripts. 

    ![][17]

3. Abra o script para edição e adicionar o seguinte código:

        using UnityEngine;
        using System.Collections;
        
        public class CameraController : MonoBehaviour {
        
            public GameObject player;
        
            private Vector3 offset;
        
            void Start ()
            {
                offset = transform.position - player.transform.position;
            }
            
            void LateUpdate ()
            {
                transform.position = player.transform.position + offset;
            }
        }
    
5. Num ambiente de unidade - arraste a variável Media Player ranhura Player para o objeto de câmara principais para que as duas estão associadas um do outro. 

    ![][18]

6. Agora se visitas reproduzir no editor de unidade e rodar o objeto de leitor bola, em seguida, irá ver a câmara seguir-na circulação.  

### <a name="setting-up-the-play-area"></a>Configurar a área de reprodução
Os passos abaixo são do [tutorial de unidade](https://unity3d.com/learn/tutorials/projects/roll-a-ball/setting-up-the-play-area?playlist=17141). Vamos criar as paredes adjacente terreno para que o objeto de leitor bola não largar desativar a área de reproduzir na sua circulação. 

1. Clique em **Criar -> Criar vazio -> jogo objeto** e o nome **paredes**

    ![][19]

2. Neste objeto paredes - crie um novo **objeto 3D -> cubo** e o nome "Parede Oeste". 

    ![][20]

3. Atualize a **transformação -> posição** e **transformar -> escala** para este objecto oeste parede. 

    ![][21]

4. Duplica parede oeste para criar uma **parede Leste** com a posição de transformação atualizados e a escala. 

    ![][22]

5. Duplica parede Leste para criar uma **parede da América do Norte** com a posição de transformação atualizada & escala. 

    ![][23]

6. Duplicar parede da América do Norte e crie uma **parede Sul** com a posição de transformação atualizada & escala. 

    ![][24]

### <a name="creating-collectible-objects"></a>Criar Collectible objetos
Os passos abaixo são do [tutorial de unidade](https://unity3d.com/learn/tutorials/projects/roll-a-ball/creating-collectables?playlist=17141). Vamos criar alguns objectos apelativa apelativos que irão constituir o conjunto de objetos collectible que o objeto de leitor bola necessita de recolher por choque com os mesmos. 

1. Criar um novo **objeto de cubo 3D** e o nome recolha. 

2. Ajustar a **transformação -> rotação** & **transformar -> a escala** do objeto escolha. 

    ![][25]

3. Criar e anexar um **Novo c# Script** chamado **rotação** para o objeto de escolha. Certifique-se colocar o script na pasta de Scripts. 

    ![][26]

4. Abra este script para edição e atualize-o para ser o seguinte: 

        using UnityEngine;
        using System.Collections;
        
        public class Rotator : MonoBehaviour {
        
            void Update () 
            {
                transform.Rotate (new Vector3 (15, 30, 45) * Time.deltaTime);
            }
        }

5. Agora visitas rodar o modo de reprodução no Editor de unidade e a apresentação de recolha de objeto no respetivo eixo.

6. Criar uma nova pasta denominada **Prefabs** 

    ![][27]

7. Arraste o objeto de **recolha** e colocá-la na pasta Prefabs.

    ![][28]

8. Crie um novo **objeto jogo vazio** denominado **Pickups**. Repor a sua posição à origem e, em seguida, arraste o objeto recolha neste objeto jogo.  

    ![][29]

9. Duplicar o objeto de **recolha** e afaste-lo no objeto **terra** em redor do objeto de **leitor** ao atualizar os valores **X & Z do Transform.Position** corretamente. 

    ![][30]

10. Criar um **novo material** denominado **recolha** e atualize-o para ser vermelho cores ao atualizar a **propriedade Albedo** semelhantes ao fez para atualizar o objeto de raiz. 

    ![][31]

11. Aplica o material a todos os objetos de recolha 4.

    ![][32]

### <a name="collecting-the-pickup-objects"></a>Recolher a recolha objetos
Os passos abaixo são do [tutorial de unidade](https://unity3d.com/learn/tutorials/projects/roll-a-ball/collecting-pick-up-objects?playlist=17141). Vamos irão atualizar o leitor para que seja possível 'recolher' os objetos recolha por choque com os mesmos. 

1. Abra a configurar o script **PlayerController** anexado ao objeto Player para edição e atualizar para o seguinte procedimento:  

        using UnityEngine;
        using System.Collections;
        
        public class PlayerController : MonoBehaviour {
        
            public float speed;
        
            private Rigidbody rb;
        
            void Start ()
            {
                rb = GetComponent<Rigidbody>();
            }
        
            void FixedUpdate ()
            {
                float moveHorizontal = Input.GetAxis ("Horizontal");
                float moveVertical = Input.GetAxis ("Vertical");
        
                Vector3 movement = new Vector3 (moveHorizontal, 0.0f, moveVertical);
        
                rb.AddForce (movement * speed);
            }
        
            void OnTriggerEnter(Collider other) 
            {
                if (other.gameObject.CompareTag ("Pick Up"))
                {
                    other.gameObject.SetActive (false);
                }
            }
        }

2. Criar uma nova **etiqueta** denominado **Escolha para cima** (tem de corresponder o que é no script)  

    ![][33]
    
    ![][34]

3. Aplica nesta **etiqueta** ao objeto recolha Prefab. 

    ![][35]

4. Ative a caixa de verificação **IsTrigger** para o objeto Prefab.

    ![][36]

5. Adicione um corpo rígido a recolha Prefab objeto. Optimização do desempenho podemos irá atualizar o collider estático que é utilizada para um collider dinâmico. 

    ![][37]
  
6. Por fim, verifique a propriedade **IsKinematic** para o objeto prefab. 

    ![][38]

7. Acertos **Reproduzir** no editor de unidade e poderão jogo este **filmar uma bola** ao mover o objeto de leitor utilizar teclas do teclado para entrada de direção. 

### <a name="updating-the-game-for-mobile-play"></a>Atualizar o jogo para ser reproduzido móvel
As secções acima concluiu o tutorial de unidade básico. Agora podemos irá modificar o jogo para torná-lo dispositivo móvel amigável. Tenha em atenção que utilizámos entrada do teclado para o jogo até ao momento para testar a ligação. Agora podemos irá modificá-lo para que recomendamos pode controlar o leitor utilizando movimento do telefone, ou seja utilizar acelerómetro como a entrada. 

Abrir configurar o script **PlayerController** para edição e atualizar o método de **FixedUpdate** a utilizar o movimento a partir do acelerómetro para mover o objeto do leitor. 

        void FixedUpdate()
        {
            //float moveHorizontal = Input.GetAxis("Horizontal");
            //float moveVertical = Input.GetAxis("Vertical");
            //Vector3 movement = new Vector3(moveHorizontal, 0.0f, moveVertical);
            rb.AddForce(Input.acceleration.x * Speed, 0, -Input.acceleration.z * Speed);
        }

Neste tutorial conclui uma criação de jogo básica com unidade e pode implementar isto num dispositivo da sua preferência para reproduzir o jogo. 

<!-- Images -->
[1]: ./media/mobile-engagement-unity-roll-a-ball/1.png  
[2]: ./media/mobile-engagement-unity-roll-a-ball/2.png
[3]: ./media/mobile-engagement-unity-roll-a-ball/3.png
[4]: ./media/mobile-engagement-unity-roll-a-ball/4.png
[5]: ./media/mobile-engagement-unity-roll-a-ball/5.png
[6]: ./media/mobile-engagement-unity-roll-a-ball/6.png
[7]: ./media/mobile-engagement-unity-roll-a-ball/7.png
[8]: ./media/mobile-engagement-unity-roll-a-ball/8.png
[9]: ./media/mobile-engagement-unity-roll-a-ball/9.png  
[10]: ./media/mobile-engagement-unity-roll-a-ball/10.png    
[11]: ./media/mobile-engagement-unity-roll-a-ball/11.png    
[12]: ./media/mobile-engagement-unity-roll-a-ball/12.png
[13]: ./media/mobile-engagement-unity-roll-a-ball/13.png
[14]: ./media/mobile-engagement-unity-roll-a-ball/14.png
[15]: ./media/mobile-engagement-unity-roll-a-ball/15.png
[16]: ./media/mobile-engagement-unity-roll-a-ball/16.png
[17]: ./media/mobile-engagement-unity-roll-a-ball/17.png
[18]: ./media/mobile-engagement-unity-roll-a-ball/18.png
[19]: ./media/mobile-engagement-unity-roll-a-ball/19.png    
[20]: ./media/mobile-engagement-unity-roll-a-ball/20.png    
[21]: ./media/mobile-engagement-unity-roll-a-ball/21.png    
[22]: ./media/mobile-engagement-unity-roll-a-ball/22.png    
[23]: ./media/mobile-engagement-unity-roll-a-ball/23.png    
[24]: ./media/mobile-engagement-unity-roll-a-ball/24.png    
[25]: ./media/mobile-engagement-unity-roll-a-ball/25.png    
[26]: ./media/mobile-engagement-unity-roll-a-ball/26.png    
[27]: ./media/mobile-engagement-unity-roll-a-ball/27.png    
[28]: ./media/mobile-engagement-unity-roll-a-ball/28.png    
[29]: ./media/mobile-engagement-unity-roll-a-ball/29.png    
[30]: ./media/mobile-engagement-unity-roll-a-ball/30.png    
[31]: ./media/mobile-engagement-unity-roll-a-ball/31.png    
[32]: ./media/mobile-engagement-unity-roll-a-ball/32.png    
[33]: ./media/mobile-engagement-unity-roll-a-ball/33.png    
[34]: ./media/mobile-engagement-unity-roll-a-ball/34.png    
[35]: ./media/mobile-engagement-unity-roll-a-ball/35.png    
[36]: ./media/mobile-engagement-unity-roll-a-ball/36.png    
[37]: ./media/mobile-engagement-unity-roll-a-ball/37.png    
[38]: ./media/mobile-engagement-unity-roll-a-ball/38.png    
[51]: ./media/mobile-engagement-unity-roll-a-ball/new-project.png
[52]: ./media/mobile-engagement-unity-roll-a-ball/new-project-properties.png
[53]: ./media/mobile-engagement-unity-roll-a-ball/save-scene.png

    
    
    
    
    
    
    
    
    
    
    
    
