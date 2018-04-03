主要有三个脚本文件

挂在摄像头上的Models.cs
using UnityEngine;
using System.Collections;
using System.Collections.Generic;
using Com.Mygame;

public class Models : MonoBehaviour
{

    GameSceneController mygame;
    //boat
    int state = 0;
    GameObject Boat;
    Vector3 boatscale = new Vector3(4f, 0.3f, 2.0f);  //boat's size;
    Vector3 boatStartpos = new Vector3(-6.0f, 0f, 0f);//设置边缘
    Vector3 boatEndpos = new Vector3(6.0f, 0.0f, 0.0f);//设置边缘
    // sits on the boat
    Vector3 boatsit1 = new Vector3(-0.25f, 3.45f, 0.0f); //  localposition is care with the scale
    Vector3 boatsit2 = new Vector3(0.25f, 3.45f, 0.0f);
    // others about boats
    float boatspeed = 1.0f;
    int boatnumber = 0;
    int Ifboatsit1have = 0;
    int Ifboatsit2have = 0;
    //river


    //Devils
    List<GameObject> devils = new List<GameObject>();
    Vector3[] devilStartpos = new Vector3[3]{
        new Vector3(-10f,1.15f,-3f),
        new Vector3(-10f,1.15f,-2f),
        new Vector3(-10f,1.15f,-1f)
    };//初始化牧师的位置
    Vector3[] devilEndpos = new Vector3[3]{
        new Vector3(10f,1.15f,-3f),
        new Vector3(10f,1.15f,-2f),
        new Vector3(10f,1.15f,-1f)
    };//初始化恶魔的位置
    Vector3[] devilScale = new Vector3[3]{
        new Vector3(),
        new Vector3(),
        new Vector3()
    };
    int[] DevilsState = new int[3] { 0, 0, 0 }; // 0-startside 1-endside 2-onboat
    int[] Devilssite = new int[3] { 0, 0, 0 }; //0表示 nosite 1-1site 2-2site

    int devilStartside = 3;
    int devilEndside = 0;

    //Priest
    List<GameObject> priests = new List<GameObject>();
    Vector3[] priestStartpos = new Vector3[3]{
        new Vector3(-10f,1.15f,1f),
        new Vector3(-10f,1.15f,2f),
        new Vector3(-10f,1.15f,3f)
    };
    Vector3[] prisetEndpos = new Vector3[3]{
        new Vector3(10f,1.15f,1f),
        new Vector3(10f,1.15f,2f),
        new Vector3(10f,1.15f,3f)
    };
    Vector3[] priestScale = new Vector3[3]{
        new Vector3(),
        new Vector3(),
        new Vector3()
    };

    int[] PriestState = new int[3] { 0, 0, 0 }; // 0-startside 1-endside 2-onboat
    int[] Priestsite = new int[3] { 0, 0, 0 };

    int priestStartside = 3;
    int priestEndside = 0;

    // startside
    GameObject startside;
    Vector3 startsidepos = new Vector3(-10.0f, 0.0f, 0.0f);
    Vector3 startsidescale = new Vector3(4f, 1f, 8f);
    // endside
    GameObject endside;
    Vector3 endsidepos = new Vector3(10.0f, 0.0f, 0.0f);
    Vector3 endsidescale = new Vector3(4f, 1f, 8f);
    //设置两个海岸线
    // Use this for initialization
    void Start()
    {
        mygame = GameSceneController.GetInstance();
        mygame.setGenGameObject(this);
        InsAll();
    }

    // Update is called once per frame
    void Update()
    {

        float step = boatspeed * Time.deltaTime;
        if (state == 2)
        {
            Boat.transform.position = Vector3.MoveTowards(Boat.transform.position, boatEndpos, boatspeed);
            if (Boat.transform.position == boatEndpos) state = 1;
        }
        else if (state == 3)
        {
            Boat.transform.position = Vector3.MoveTowards(Boat.transform.position, boatStartpos, boatspeed);
            if (Boat.transform.position == boatStartpos) state = 0;
        }
    }



    // ins all gameobjects
    void InsAll()
    {
        //设置两岸
        startside = GameObject.CreatePrimitive(PrimitiveType.Cube);
        startside.transform.localScale = startsidescale;
        startside.transform.position = startsidepos;
        startside.GetComponent<Renderer>().material.color = Color.green;
        endside = GameObject.CreatePrimitive(PrimitiveType.Cube);
        endside.transform.localScale = endsidescale;
        endside.transform.position = endsidepos;
        endside.GetComponent<Renderer>().material.color = Color.green;
        //设置船只    
        Boat = GameObject.CreatePrimitive(PrimitiveType.Cube);
        Boat.transform.position = boatStartpos;
        Boat.transform.localScale = boatscale;
        Boat.GetComponent<Renderer>().material.color = Color.grey;
        //设置魔鬼与牧师
        for (int i = 0; i < 3; i++)
        {
            GameObject devil = GameObject.CreatePrimitive(PrimitiveType.Sphere);
            devil.GetComponent<Renderer>().material.color = Color.black;
            devil.transform.position = devilStartpos[i];
            devils.Add(devil);
            GameObject priest = GameObject.CreatePrimitive(PrimitiveType.Sphere);
            priest.transform.position = priestStartpos[i];
            priests.Add(priest);

        }

        //light !!cool
        GameObject light = new GameObject("The light");
        Light lightComp = light.AddComponent<Light>();
        lightComp.type = LightType.Directional;
        light.transform.position = new Vector3();
        Quaternion Q;
        Q = Quaternion.Euler(20.0f, 0.0f, 0.0f);
        light.transform.rotation = Q;

    }

    public void DevilGoOnBoat()
    { // must write by public ,or the interface can not use it
        if (mygame.wl != WinOrLose.Gaming)
        {
            return;
        }
        if (state == 0)
        { //startside
            if (boatnumber != 2)
            {
                for (int i = 0; i < 3; i++)
                {
                    if (DevilsState[i] == 0)
                    {

                        DevilsState[i] = 2;//mean now this devil's state is on boat

                        devils[i].transform.parent = Boat.transform;

                        if (Ifboatsit1have == 0 && Ifboatsit2have == 0)
                        {
                            devils[i].transform.localPosition = boatsit1;
                            Devilssite[i] = 1;
                            Ifboatsit1have = 1;
                        }
                        else if (Ifboatsit1have == 1 && Ifboatsit2have == 0)
                        {
                            devils[i].transform.localPosition = boatsit2;
                            Devilssite[i] = 2;
                            Ifboatsit2have = 1;
                        }
                        else if (Ifboatsit1have == 0 && Ifboatsit2have == 1)
                        {
                            devils[i].transform.localPosition = boatsit1;
                            Devilssite[i] = 1;
                            Ifboatsit1have = 1;
                        }

                        devilStartside--;
                        boatnumber++;
                        break;
                    }
                }
            }
        }
        else if (state == 1)
        { // endside
            if (boatnumber != 2)
            {
                for (int i = 0; i < 3; i++)
                {
                    if (DevilsState[i] == 1)
                    {

                        DevilsState[i] = 2;//mean now this devil's state is on boat
                        devils[i].transform.parent = Boat.transform;

                        if (Ifboatsit1have == 0 && Ifboatsit2have == 0)
                        {
                            devils[i].transform.localPosition = boatsit1;
                            Devilssite[i] = 1;
                            Ifboatsit1have = 1;
                        }
                        else if (Ifboatsit1have == 1 && Ifboatsit2have == 0)
                        {
                            devils[i].transform.localPosition = boatsit2;
                            Devilssite[i] = 2;
                            Ifboatsit2have = 1;
                        }
                        else if (Ifboatsit1have == 0 && Ifboatsit2have == 1)
                        {
                            devils[i].transform.localPosition = boatsit1;
                            Devilssite[i] = 1;
                            Ifboatsit1have = 1;
                        }

                        devilEndside--;
                        boatnumber++;
                        break;
                    }
                }
            }
        }
    }

    public void DevilGoOffBoat()
    {
        if (mygame.wl != WinOrLose.Gaming)
        {
            return;
        }
        if (state == 0)
        { // startside
            for (int i = 0; i < 3; i++)
            {
                if (DevilsState[i] == 2)
                {
                    devils[i].transform.parent = null;
                    devils[i].transform.position = devilStartpos[i];
                    DevilsState[i] = 0;

                    if (Devilssite[i] == 1)
                        Ifboatsit1have = 0;
                    else if (Devilssite[i] == 2)
                        Ifboatsit2have = 0;
                    Devilssite[i] = 0;

                    devilStartside++;
                    boatnumber--;
                    break;
                }
            }
        }
        else if (state == 1)
        { // endside
            for (int i = 0; i < 3; i++)
            {
                if (DevilsState[i] == 2)
                {
                    devils[i].transform.parent = null;
                    devils[i].transform.position = devilEndpos[i];
                    DevilsState[i] = 1;

                    if (Devilssite[i] == 1)
                        Ifboatsit1have = 0;
                    else if (Devilssite[i] == 2)
                        Ifboatsit2have = 0;
                    Devilssite[i] = 0;

                    devilEndside++;
                    boatnumber--;
                    break;
                }
            }
        }
    }

    public void PriestGoOnBoat()
    {
        if (mygame.wl != WinOrLose.Gaming)
        {
            return;
        }
        if (state == 0)
        { //startside
            if (boatnumber != 2)
            {
                for (int i = 0; i < 3; i++)
                {
                    if (PriestState[i] == 0)
                    {

                        PriestState[i] = 2;//mean now this devil's state is on boat
                        priests[i].transform.parent = Boat.transform;

                        if (Ifboatsit1have == 0 && Ifboatsit2have == 0)
                        {
                            priests[i].transform.localPosition = boatsit1;
                            Priestsite[i] = 1;
                            Ifboatsit1have = 1;
                        }
                        else if (Ifboatsit1have == 1 && Ifboatsit2have == 0)
                        {
                            priests[i].transform.localPosition = boatsit2;
                            Priestsite[i] = 2;
                            Ifboatsit2have = 1;
                        }
                        else if (Ifboatsit1have == 0 && Ifboatsit2have == 1)
                        {
                            priests[i].transform.localPosition = boatsit1;
                            Priestsite[i] = 1;
                            Ifboatsit1have = 1;
                        }

                        priestStartside--;
                        boatnumber++;
                        break;
                    }
                }
            }
        }
        else if (state == 1)
        { // endside
            if (boatnumber != 2)
            {
                for (int i = 0; i < 3; i++)
                {
                    if (PriestState[i] == 1)
                    {

                        PriestState[i] = 2;//mean now this devil's state is on boat
                        priests[i].transform.parent = Boat.transform;

                        if (Ifboatsit1have == 0 && Ifboatsit2have == 0)
                        {
                            priests[i].transform.localPosition = boatsit1;
                            Priestsite[i] = 1;
                            Ifboatsit1have = 1;
                        }
                        else if (Ifboatsit1have == 1 && Ifboatsit2have == 0)
                        {
                            priests[i].transform.localPosition = boatsit2;
                            Priestsite[i] = 2;
                            Ifboatsit2have = 1;
                        }
                        else if (Ifboatsit1have == 0 && Ifboatsit2have == 1)
                        {
                            priests[i].transform.localPosition = boatsit1;
                            Priestsite[i] = 1;
                            Ifboatsit1have = 1;
                        }

                        priestEndside--;
                        boatnumber++;
                        break;
                    }
                }
            }
        }
    }

    public void PriestGoOffBoat()
    {
        if (mygame.wl != WinOrLose.Gaming)
        {
            return;
        }
        if (state == 0)
        { // startside
            for (int i = 0; i < 3; i++)
            {
                if (PriestState[i] == 2)
                {
                    priests[i].transform.parent = null;
                    priests[i].transform.position = priestStartpos[i];
                    PriestState[i] = 0;
                    if (Priestsite[i] == 1)
                        Ifboatsit1have = 0;
                    else if (Priestsite[i] == 2)
                        Ifboatsit2have = 0;
                    Priestsite[i] = 0;
                    priestStartside++;
                    boatnumber--;
                    break;
                }
            }
        }
        else if (state == 1)
        { // endside
            for (int i = 0; i < 3; i++)
            {
                if (PriestState[i] == 2)
                {
                    priests[i].transform.parent = null;
                    priests[i].transform.position = prisetEndpos[i];
                    PriestState[i] = 1;

                    if (Priestsite[i] == 1)
                        Ifboatsit1have = 0;
                    else if (Priestsite[i] == 2)
                        Ifboatsit2have = 0;
                    Priestsite[i] = 0;
                    priestEndside++;
                    boatnumber--;
                    break;
                }
            }
        }
    }

    public void BoatGo()
    {
        if (mygame.wl != WinOrLose.Gaming)
        {
            return;
        }
        if (boatnumber != 0)
        {
            check();
            if (state == 0)
            {
                // from startpos to endpos,move
                state = 2;
            }
            else if (state == 1)
            {
                state = 3;
                // form endpos to startpos,move
            }
        }
    }


    //判断游戏是否可以继续
    public void check()
    {
        //lose check
        print(devilStartside);
        print(devilEndside);
        print(priestStartside);
        print(priestEndside);
        if (state == 0)
        {
            if ((devilStartside > priestStartside && priestStartside != 0) || ((3 - devilStartside) > (3 - priestStartside) && (3 - priestStartside) != 0))
            {
                mygame.wl = WinOrLose.Lose;
                return;
            }
        }
        else if (state == 1)
        {
            if ((devilEndside > priestEndside && priestEndside != 0) || ((3 - devilEndside) > (3 - priestEndside) && (3 - priestEndside) != 0))
            {
                mygame.wl = WinOrLose.Lose;
                return;
            }
        }
        //win check
        if (devilStartside == 0 && priestStartside == 0) mygame.wl = WinOrLose.Win;

    }
}







Controller.cs

using UnityEngine;
using System.Collections;
using Com.Mygame;

public class Controller : MonoBehaviour
{
    GameSceneController mygame;
    ImyAction actions;
    float blank = 10;
    float BottonW = Screen.width / 8;
    float BottonH = Screen.height / 16;
    float firstBx = Screen.width / 8;
    float firstBy = Screen.height / 16;
    private string str = "RESET！";
    // Use this for initialization
    void Start()
    {

        mygame = GameSceneController.GetInstance();
        actions = GameSceneController.GetInstance() as ImyAction;
    }

    void OnGUI()
    {

        if (GUI.Button(new Rect(firstBx, firstBy, BottonW, BottonH), "devil Go boat"))
        {
            actions.DevilGoOnBoat();
        }
        if (GUI.Button(new Rect(firstBx + BottonW + blank, firstBy, BottonW, BottonH), "devil Out boats"))
        {
            actions.DevilGoOffBoat();
        }
        if (GUI.Button(new Rect(firstBx, firstBy + BottonH + blank, BottonW, BottonH), "priest Go boats"))
        {
            actions.PriestGoOnBoat();
        }
        if (GUI.Button(new Rect(firstBx + BottonW + blank, firstBy + BottonH + blank, BottonW, BottonH), "priest Out boats"))
        {
            actions.PriestGoOffBoat();
        }
        if (GUI.Button(new Rect(firstBx, firstBy + (BottonH + blank) * 2, BottonW * 2 + blank, BottonH * 2), "Boat Go On"))
        {
            actions.BoatGo();
            if (mygame.wl == WinOrLose.Gaming) str = "RESET";
            else if (mygame.wl == WinOrLose.Lose) str = "Lose！";
            else if (mygame.wl == WinOrLose.Win) str = "Win！";
        }

        if (GUI.Button(new Rect(Screen.width - BottonW / 2 - 400, firstBy + 50, BottonW / 2 + 100, BottonH), str))
        {
            Application.LoadLevel(Application.loadedLevelName);
            mygame.wl = WinOrLose.Gaming;
        }

    }
}






View.cs
using UnityEngine;
using System.Collections;
using Com.Mygame;

namespace Com.Mygame
{

    public enum WinOrLose { Win, Lose, Gaming };

    public interface ImyAction
    {
        void DevilGoOnBoat();
        void DevilGoOffBoat();
        void PriestGoOnBoat();
        void PriestGoOffBoat();
        void BoatGo();
        //魔鬼和牧师上下船还有行船五个操作
    }

    public class GameSceneController : System.Object, ImyAction
    {
        private static GameSceneController _instance;
        private View _base_code;
        private Models _gen_game_object;

        public WinOrLose wl = WinOrLose.Gaming;

        public static GameSceneController GetInstance()
        {
            if (null == _instance)
            {
                _instance = new GameSceneController();
            }
            return _instance;
        }

        public View getBaseCode()
        {
            return _base_code;
        }

        internal void setBaseCode(View bc)
        {
            if (null == _base_code)
            {
                _base_code = bc;
            }
        }

        public Models getGenGameObject()
        {
            return _gen_game_object;
        }

        internal void setGenGameObject(Models g)
        {
            if (_gen_game_object == null)
            {
                _gen_game_object = g;
            }
        }

        public void DevilGoOnBoat()
        {
            _gen_game_object.DevilGoOnBoat();
        }

        public void DevilGoOffBoat()
        {
            _gen_game_object.DevilGoOffBoat();
        }

        public void PriestGoOnBoat()
        {
            _gen_game_object.PriestGoOnBoat();
        }

        public void PriestGoOffBoat()
        {
            _gen_game_object.PriestGoOffBoat();
        }

        public void BoatGo()
        {
            _gen_game_object.BoatGo();
        }
    }
}

public class View : MonoBehaviour
{
    void Start()
    {
        this.transform.position = new Vector3(0.0f, 5.0f, -12.0f);
        GameSceneController mygame = GameSceneController.GetInstance();
        mygame.setBaseCode(this);
    }
}

//代码借鉴师兄博客。
