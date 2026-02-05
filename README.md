# Drag and Drop to drag and drop UI elements in Unity 
 

<video src="https://github.com/alfredo1995/drag-and-drop/assets/71193893/48a8f7c6-8854-4a67-827f-9229bd0a5951" width="500%" controls>
</video>


<br> 

IDropHandler

    IDropHandler é uma interface usada para implementar funcionalidades de manipulação de arrasto e soltar (drag and drop) entre objetos da cena

0° Criar Canvas do tipo imagem 
 
    Criar Canvas do tipo imagem > Create Empty > GameOject > Image > arrastar os sprites (apple and box)


1° Capturar o evento de clique do mouse no objeto 

	//Criar um script e anexar no GameOject apple (drag) que será arastado ate o GameOject box (drop)

	*Importar o sitstema de evento 
	*Herdar a class IpointerDownHandler (manipulador de entrada do mouse precionado ou clicado)
	*Criar função que será chamada quando o mouse for precionado, enquanto estiver em cima do objeto
	*fazer um log de depuração para fins de test function

	using UnityEngine.EventSystems;

	public class DragDrop : MonoBehaviour, IPointerDownHandler 
	{
	public void OnPointerDown(PointerEventDate eventData)
	{
		Debug.Log("OnPointerDown");
	}}


2° Capturar o evento(Calling Funciton) de quando o mouse estiver iniciando o arrasto e terminando de arrastar o GameOject 


        //Criar funções para chamar os eventos abaixo:
	
	*Herdar a interface IBeginDragHandler  (manipulador de entrada do mouse quando começar arrastar)
	*Herdar a interface IEndDragHandler    (manipulador de entrada do mouse quando terminar de arrastar)
	*Herdar a interface IDragHandler       (manipulador de entrada do mouse para conseguir arrasta a cada frame)



	public class DragDrop : MonoBehaviour, IPointerDownHandler, IBeginDragHandler, IEndDragHandler, IDragHandler
        {
      
        public void OnBeginDrag(PointerEventData eventData)
        {
     	   Debug.Log("OnBeginDrag");
        }

   	public void OnEndDrag(PointerEventData eventData)
   	{
  	   Debug.Log("OnEndDrag");
  	}	

   	public void OnDrag(PointerEventData eventData)
   	{
           Debug.Log("OnDrag");
        }

   	public void OnPointerDown(PointerEventData eventData)
  	{
           Debug.Log("OnDownDrag");
        }}



3° Manipular o movimento do objeto na função chamando o metodo de arrastar

	*referenciar o componente ReactTransform (moviment) 

	*criar o metodo awake() para inicializar o objeto quando instaciado(criado)
	*manipular a posição acessando o componente - reactTransform = GetComponent<ReactTransform>();

	*modificar a posição ancora e nos dados do evento pegar o delta (evento quando um objeto da UI está sendo arrastado)

	*ancora (a posição ancorada especifica onde um objeto sera posicionado em relação a um ponto específico)
	*delta  (mudança entre valores em quadros consecutivos, seja no tempo, posição, rotação, etc)

	*reactTransform.anchoredPosition += eventData.delta;
	

	public class DragDrop : MonoBehaviour, IPointerDownHandler, IBeginDragHandler, IEndDragHandler, IDragHandler
	{
         
        private RectTransform rectTransform;

        private void Awake()
        {
	   rectTransform = GetComponent<RectTransform>();
        }

        public void OnBeginDrag(PointerEventData eventData)
        {
           Debug.Log("OnBeginDrag");
        }

   	public void OnEndDrag(PointerEventData eventData)
  	{
            Debug.Log("OnEndDrag");
        }

        public void OnDrag(PointerEventData eventData)
        {
   	     Debug.Log("OnDrag");
	     rectTransform.anchoredPosition += eventData.delta;
        }

 	public void OnPointerDown(PointerEventData eventData)
   	{
   	     Debug.Log("OnDownDrag");
        }}
	

4° Alterar o movimento exatamente na mesma taxa (proporcionalmente 1:1) que o ponteiro do mouse ou toque  (tamanho da tela ou da resolução)

	//Este método é chamado continuamente enquanto o usuário arrasta o objeto.
        //Se você arrastar o ponteiro do mouse 10 pixels em uma direção, o objeto também se moverá 10 pixels nessa mesma direção.
	
	*referênciar o componente Canvas 	

	*atribuindo referência a um componente RectTransform do objeto da UI
	*anchoredPosition: É usado para mover o objeto mantendo sua relação com um ponto fixo.
	*+=: Significa que estamos adicionando um valor à posição ancorada atual do objeto.
	*eventData.delta: É a diferença entre a posição atual do arraste e a posição anterior do arraste. (vector q representa a quantidade de movimento que ocorreu.)
	*/ canvas.scaleFactor: Dividindo o eventData.delta pelo scaleFactor do objeto canvas. (Feito para levar em consideração a escala do canvas.)

	//ajustando a posição ancorada do objeto da UI com base na diferença de arrasto (movimento) do ponteiro do mouse ou toque, levando em consideração o fator de escala do canvas. 


        [SerializeField] private Canvas canvas;

	public void OnDrag(PointerEventData eventData)
	{
 	   rectTransform.anchoredPosition += eventData.delta / canvas.scaleFactor;
	}


5° Manipular componente CanvasGroup p/ controlar a visibilidade e a interatividade de um grupo de objetos dentro de um Canvas

	*Adicionar um novo componente do tipo "canvas group" no gameobject apple (drag)

	*referenciar componente CanvasGroup no script DragDrop.cs

	*Criar metodo Awake chamado quando o objeto ao qual o script está anexado é inicializado.
	*Acessar e manipular o componente CanvasGroup por meio da variável canvasGroup.

	private CanvasGroup canvasGroup;

	private void Awake()
	{
	   canvasGroup = GetComponent<CanvasGroup>();
	}


6° Desabilitar o item arrastavel na captura dos eventos (começando e terminando o arrasto)

	//raycast é uma operação que permite detectar colisões e interações entre objetos.

 	//quando começar arrastar, entraremos no CanvasGroup e desabilitar o raycast	
	public void OnBeginDrag(PointerEventData eventData)
	{
	   //modificar a cor
	   canvasGroup.alpha = .6f;
	   canvasGroup.blocksRaycasts = false;
	}
	
	//quando terminar arrastar, entraremos no CanvasGroup e habilitar o raycast 
	public void OnEndDrag(PointEventData eventData)
	{
	    canvasGroup.alpha = .1f;
	    canvasGroup.blocksRaycasts = true;


7° Interface IDropHandler usada nos eventos de arrastar e soltar para manipular o comportamento quando um objeto é solto em um alvo de soltar.


	//A assinatura de um método em programação se refere à sua estrutura e formato (interface) 
	//incluindo o nome do método, os parâmetros que ele recebe e o tipo de retorno (assinatura)

	
	*Criar um novo script ItemSlot.cs e anexar no gameobject box (slot) 	
	*Criar função OnDrop, que será chamada quando um objeto for inserido

	public class ItemSlot : MonoBehaviour, IDropHandler

	public void OnDrop(PointerEventData eventData)
	{
           Debug.log("objeto e solto em um alvo de soltar");
	}


8° Posicionar o objeto arrastado no local do objeto de soltar (arrastado e liberado)

	
	//Acessar o metodo OnDrop quando um objeto é solto (arrastado e liberado) sobre um elemento que implementa a interface ID

	//if (Verificar se há um objeto arrastado válido.)

	//Depois obter o componente RectTransform do objeto arrastado. Permite que você acesse e modifique as propriedades do objeto arrastado.

	// atribuição (a posição do objeto arrastado será ajustada para ser a mesma que a posição do objeto onde ele foi solto.)

	public void OnDrop(PointerEventData eventData) 
        {
           if (eventData.pointerDrag != null)
           {
              eventData.pointerDrag.GetComponent<RectTransform>().anchoredPosition = GetComponent<RectTransform>().anchoredPosition;
           }
        }

 
 
 
