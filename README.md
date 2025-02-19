using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using TMPro;
using UnityEngine.SceneManagement;

public class touchDetection : MonoBehaviour
{
    int NumOfCoins;
    int i = 1;
    public int playerHealth = 3;
    public GameObject[] hearts;
    public TMP_Text CoinNumText;

    private void Start()
    {
        CoinNumText.text = "X" + NumOfCoins.ToString();
    }

    private void OnTriggerEnter2D(Collider2D collision)
    {
        if (collision.CompareTag("Coin"))
        {
            NumOfCoins += 1;
            CoinNumText.text = "X" + NumOfCoins.ToString();
            Destroy(collision.gameObject);
        }
        else if (collision.CompareTag("Spike"))
        {
            takeDamage();
        }
        else if (collision.CompareTag("PowerUp"))
        {
            GainHealth();
            Destroy(collision.gameObject);
        }
        else if (collision.CompareTag("TreasureBox"))
        {
            CollectTreasureBox();
            Destroy(collision.gameObject);
        }
    }

    public void takeDamage()
    {
        if (playerHealth > 0)
        {
            playerHealth -= 1;
            hearts[hearts.Length - i].gameObject.SetActive(false);
            i++;
            if (playerHealth <= 0)
            {
                SceneManager.LoadScene(SceneManager.GetActiveScene().buildIndex);
            }
        }
    }

    private void GainHealth()
    {
        if (playerHealth < 3)
        {
            playerHealth += 1;
            i--;
            hearts[hearts.Length - i].gameObject.SetActive(true);
        }
    }

    private void CollectTreasureBox()
    {
        NumOfCoins += 10;
        CoinNumText.text = "X" + NumOfCoins.ToString();
    }
}






using UnityEngine;
using System.Collections;
using System.Collections.Generic;

public class EnemySpawner : MonoBehaviour
{
    private GameObject mario;
    private bool EnemySpawned = false;
    private GameObject SpawnedEnemy;
    public GameObject EnemyPrefab;
    public Transform SpawnPoint;
    public void Start()
    {
        mario = GameObject.FindGameObjectWithTag("Player");
    }
    public void OnTriggerEnter2D(Collider2D collision)
    {
        if (collision.CompareTag("Player") && !EnemySpawned)
        {
            SpawnEnemy();
            EnemySpawned = true;
        }
    }



    public void SpawnEnemy()
    {
        SpawnedEnemy = Instantiate(EnemyPrefab, SpawnPoint.position, Quaternion.identity);
    }

}



using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class EnemyDamage : MonoBehaviour
{
    private bool hasDealtDamage = false;

    private void OnTriggerEnter2D(Collider2D collision)
    {
        if (collision.CompareTag("Player") && !hasDealtDamage)
        {
            touchDetection marioHealth = collision.GetComponent<touchDetection>();
            if (marioHealth != null)
            {
                marioHealth.takeDamage();
                hasDealtDamage = true;
                Destroy(gameObject);
            }
        }
    }
}

