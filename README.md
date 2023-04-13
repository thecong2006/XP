using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;

public class XP : MonoBehaviour
{
    public int LV;
    public float currentXP; //XP hiện tại
    public float requiredXP; //XP yêu cầu

    private float lerpTimer;
    private float delayTimer;

    [Header("UI")]
    public Image frontXPbar; //thanh xp phía trước
    public Image backXPbar; // thanh xp phía sau

    //Các biện hệ số nhân để tăng XP yêu cầu khi lên cấp
    public float additionMultiplier = 300;
    public float powerMultiplier = 2;
    public float divisionMultiplier = 7;
    // Start is called before the first frame update
    void Start()
    {
        frontXPbar.fillAmount = currentXP / requiredXP; //xp hiện tại chia cho xp yêu cầu
        backXPbar.fillAmount = currentXP / requiredXP; //xp hiện tại chia cho xp yêu cầu
        requiredXP = UpRequiredXP();
    }

    // Update is called once per frame
    void Update()
    {
        UpdateXP();
        if (Input.GetKeyDown(KeyCode.A))
        {
            UpXP(5);
            if(currentXP > requiredXP)
            {
                LVUP();
            }
        }
    }
    public void UpdateXP()
    {
        float xpFraction = currentXP / requiredXP; //Chuyển hóa xp hiện tại thành phần số
        float FXP = frontXPbar.fillAmount; //Thanh xp phía trước
        if(FXP < xpFraction) //Thanh xp fillAmount phía trước nếu nhỏ phần số xpFraction
        {
            delayTimer += Time.deltaTime; //độ trễ (Time.deltaTime) để thanh máu giảm dần;
            backXPbar.fillAmount = xpFraction; //thanh xp phía sau = xp hiện tại chuyển thành phần số
            if(delayTimer > 3)
            {
                lerpTimer += Time.deltaTime;
                float percentComlete = lerpTimer / 4;
                frontXPbar.fillAmount = Mathf.Lerp(FXP, backXPbar.fillAmount, percentComlete); //thanh XP, thanh XP dưới, độ trễ - độ trễ mà thanh XP dưới đổi thanh XP trên
            }

        }
    }
    public void UpXP(float xpUP)
    {
        currentXP += xpUP;
        lerpTimer = 0f;
    }
    public void LVUP()
    {
        LV++; // khi lên cấp lv cộng 1
        frontXPbar.fillAmount = 0f; // khôi phục thanh xp = 0
        backXPbar.fillAmount = 0f; // khôi phục thanh xp sau = 0
        currentXP = Mathf.RoundToInt(currentXP - requiredXP); //điểm xp hiện tại trừ đi xp tối đa
        requiredXP = UpRequiredXP();
    }
    public void buttonXPup()
    {
        UpXP(10);
        if (currentXP > requiredXP)
        {
            LVUP();
        }
    }
    public int UpRequiredXP()
    {
        int solveForRequiredXP = 0;
        for(int levelCycle = 1; levelCycle <= LV; levelCycle++)
        {
            solveForRequiredXP += (int)Mathf.Floor(levelCycle + additionMultiplier * Mathf.Pow(powerMultiplier, levelCycle / divisionMultiplier)); //sửa dụng công thức để tính XP yêu cầu khi lên cấp
        }
        return solveForRequiredXP / 4;
    }
}
