<template>
  <link rel="stylesheet" href="style.css">
  <link href="https://fonts.googleapis.com/css2?family=Kanit:wght@400;500;600&family=Prompt:wght @400,500,600&display=swap" rel="stylesheet">  
  <div id="app">
    <h1>About Me</h1>

    
    <div class="tabs">
      <button @click="currentTab = 1" :class="{ active: currentTab === 1 }">
        About Me
      </button>
      <button @click="currentTab = 2" :class="{ active: currentTab === 2 }">
        Education
      </button>
      <button @click="currentTab = 3" :class="{ active: currentTab === 3 }">
        My Gallery
      </button>
      <button @click="currentTab = 4" :class="{ active: currentTab === 4 }">
        My Art
      </button>
    </div>


    <div v-if="currentTab === 1">
  
      <div class="student-info">
        <div class="student-image">
          <img :src="student.imageUrl" alt="Student Image" />
        </div>
        <div class="student-details" v-if="!isEditing">
          <p>
            <strong>ชื่อ:</strong> {{ student.firstName }}
            {{ student.lastName }}
          </p>
          <p><strong>รหัสนิสิต:</strong> {{ student.studentId }}</p>
          <p><strong>สาขา:</strong> {{ student.department }}</p>
          <p><strong>สถานศึกษา:</strong> {{ student.university }}</p>
        </div>

        <div v-if="isEditing">
          <h3>แก้ไขข้อมูลนิสิต</h3>
          <form @submit.prevent="updateStudent">
            <div>
              <label for="firstName">ชื่อ:</label>
              <input
                v-model="student.firstName"
                id="firstName"
                type="text"
                required
              />
            </div>
            <div>
              <label for="lastName">นามสกุล:</label>
              <input
                v-model="student.lastName"
                id="lastName"
                type="text"
                required
              />
            </div>
            <div>
              <label for="studentId">รหัสนิสิต:</label>
              <input
                v-model="student.studentId"
                id="studentId"
                type="text"
                required
              />
            </div>
            <div>
              <label for="department">สาขา:</label>
              <input
                v-model="student.department"
                id="department"
                type="text"
                required
              />
            </div>
            <div>
              <label for="university">สถานศึกษา:</label>
              <input
                v-model="student.university"
                id="university"
                type="text"
                required
              />
            </div>
            <div class="form-buttons">
              <button type="submit">บันทึกการแก้ไข</button>
              <button @click="cancelEdit" type="button">ยกเลิก</button>
            </div>
          </form>
        </div>

      
        <div class="edit-button-container" v-if="!isEditing">
          <button @click="isEditing = true">แก้ไขข้อมูล</button>
        </div>
      </div>
    </div>

    <div v-if="currentTab === 2">
      
      <div class="courses-info">
        <h2>Education</h2>

        <div
          v-for="(course, index) in courses"
          :key="index"
          class="course-item"
        >
          <div class="course-row">
            <p><strong>รหัสวิชา:</strong> {{ course.code }}</p>
            <p><strong>ชื่อวิชา:</strong> {{ course.name }}</p>
          </div>
          <div class="course-row">
            <p><strong>หน่วยกิจ:</strong> {{ course.credit }}</p>
            <p><strong>เกรด:</strong> {{ course.grade }}</p>
          </div>
          <div class="course-actions">
            <button @click="editCourse(index)">แก้ไข</button>
            <button @click="deleteCourse(index)">ลบ</button>
          </div>
        </div>


        <div v-if="isEditingCourse">
          <h3>
            {{
              editCourseIndex !== null ? "แก้ไขข้อมูลวิชา" : "เพิ่มข้อมูลวิชา"
            }}
          </h3>
          <form @submit.prevent="submitCourseForm">
            <div>
              <label for="courseCode">รหัสวิชา:</label>
              <input
                v-model="courseForm.code"
                id="courseCode"
                type="text"
                required
              />
            </div>
            <div>
              <label for="courseName">ชื่อวิชา:</label>
              <input
                v-model="courseForm.name"
                id="courseName"
                type="text"
                required
              />
            </div>
            <div>
              <label for="courseCredit">หน่วยกิจ:</label>
              <input
                v-model="courseForm.credit"
                id="courseCredit"
                type="number"
                required
              />
            </div>
            <div>
              <label for="courseGrade">เกรด:</label>
              <input
                v-model="courseForm.grade"
                id="courseGrade"
                type="text"
                required
              />
            </div>
            <div class="form-buttons">
              <button type="submit">
                {{ editCourseIndex !== null ? "บันทึกการแก้ไข" : "เพิ่มวิชา" }}
              </button>
              <button @click="cancelCourseEdit" type="button">ยกเลิก</button>
            </div>
          </form>
        </div>

           <div v-if="!isEditingCourse">
          <button @click="startCourseEdit()">เพิ่มวิชา</button>
        </div>
      </div>
    </div>

    <div v-if="currentTab === 3">

      <div class="image-gallery">
        <h2>My Gallery</h2>

        <div class="gallery">
          <div class="image-item">
            <img :src="images[currentImageIndex]" alt="Gallery Image" />
          </div>
        </div>

        <div class="navigation-buttons">
          <button @click="prevImage" :disabled="currentImageIndex === 0">
            ก่อนหน้า
          </button>
          <button
            @click="nextImage"
            :disabled="currentImageIndex === images.length - 1"
          >
            ถัดไป
          </button>
        </div>
      </div>
    </div>

    <div v-if="currentTab === 4">
    
      <div class="art-gallery">
        <h2>My Art</h2>
        <div class="art-grid">
          <div v-for="(art, index) in artworks" :key="index" class="art-item">
            <img :src="art.imageUrl" :alt="art.title" @click="openModal(art.imageUrl)" />
            <div class="art-info">
              <h3>{{ art.title }}</h3>
              <p>{{ art.description }}</p>
              <p class="art-date">{{ art.date }}</p>
              <button @click.stop="openModal(art.imageUrl)" class="view-full-btn">View Full</button>
            </div>
          </div>
        </div>
      </div>


      <div v-if="showModal" class="modal" @click="closeModal">
        <div class="modal-content" @click.stop>
          <span class="close-btn" @click="closeModal">&times;</span>
          <img :src="modalImageUrl" alt="Full size artwork" class="modal-image" />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      currentTab: 1,
      isEditing: false,
      isEditingCourse: false,
      editCourseIndex: null,
      showModal: false,
      modalImageUrl: '',
      courseForm: {
        code: "",
        name: "",
        credit: "",
        grade: "",
      },
      student: {
        firstName: "นรมน",
        lastName: "ศิลากุล",
        studentId: "6630200306",
        department: "วิทยาการคอมพิวเตอร์",
        university: "มหาวิทยาลัยเกษตรศาสตร์",
        imageUrl: "https://scontent.fbkk13-1.fna.fbcdn.net/v/t1.15752-9/486748298_1134733788425274_7560057444255203126_n.jpg?_nc_cat=105&ccb=1-7&_nc_sid=0024fc&_nc_ohc=xBDznt-zIr0Q7kNvgHNECT0&_nc_oc=AdlAuXccwY9M2wlFNWrg-xNytkaMzXx68COoAV1UJSuCeL1tOXpwEEJADoHpRwUTa9GFC2qPB2SCIGS97GVpksB5&_nc_ad=z-m&_nc_cid=1277&_nc_zt=23&_nc_ht=scontent.fbkk13-1.fna&oh=03_Q7cD1wEmHxqbt93_oUj8cmLtT6hP913050GWANwKolOxclIHrQ&oe=681632D9",
      },
      courses: [
        {
          code: "01174231	",
          name: "Introduction to Recreation",
          credit: 2,
          grade: "B+",
        },
        {
          code: "01417111",
          name: "Calculus I",
          credit: 3,
          grade: "C",
        },
        {
          code: "01418211",
          name: "Software Construction",
          credit: 3,
          grade: "D+",
        },
        {
          code: "01418214",
          name: "Practicum in Software Development",
          credit: 1,
          grade: "N",
        },
        {
          code: "01418232",
          name: "	Algorithm Design and Analysis",
          credit: 3,
          grade: "N",
        },
      ],
      images: [
        "https://scontent.fbkk12-2.fna.fbcdn.net/v/t1.15752-9/486724995_2146307352502312_1261139593503614491_n.jpg?_nc_cat=104&ccb=1-7&_nc_sid=0024fc&_nc_ohc=e30NwaWXepwQ7kNvwE5SFBb&_nc_oc=AdnzJp-y_v_6Msx51hkXdPZJUDxHWiH1YC3tZox-c5gfjuOG-JxB5DmmTYu9FzYSfXfjQj7nEHwb7nJHpe10TqQI&_nc_ad=z-m&_nc_cid=1277&_nc_zt=23&_nc_ht=scontent.fbkk12-2.fna&oh=03_Q7cD1wF7zTcCGmg6IKiq00Gu0xuyNJWeHEQeyX4N1-rKjKRKHQ&oe=68163020",
        "https://scontent.fbkk12-2.fna.fbcdn.net/v/t1.15752-9/487885331_660134966873775_5472675064241250995_n.jpg?_nc_cat=104&ccb=1-7&_nc_sid=0024fc&_nc_ohc=hwBAY4pCsMoQ7kNvwGINXw1&_nc_oc=AdktuVu2WHG5hInINi6bLoJBYeoo6IWzjkuLrZxpqWrUZ9wFvSEcZHo90WAkZhUjphvFNcZ6fnHhOdiIBlqUcrHO&_nc_ad=z-m&_nc_cid=1277&_nc_zt=23&_nc_ht=scontent.fbkk12-2.fna&oh=03_Q7cD1wEcKFx-xdcjn0fSEKfb08ZptrpSzJaGu7NzjmRpNQMNfA&oe=681617EF",
        "https://scontent.fbkk12-2.fna.fbcdn.net/v/t1.15752-9/486764722_1049437287238330_2381909296080067561_n.jpg?_nc_cat=104&ccb=1-7&_nc_sid=0024fc&_nc_ohc=pyclxAXsvYkQ7kNvwHca50t&_nc_oc=AdlhxvvrE3Qb0wo76FjvZxIg4nyVG_M_J-7pdOGZXnxULbLGKutIIW37nV2qNNxBO-DjmE6aOI9Wyt3NUc1YNlrn&_nc_ad=z-m&_nc_cid=1277&_nc_zt=23&_nc_ht=scontent.fbkk12-2.fna&oh=03_Q7cD1wEQazjn6B1ydAy4HzBZ1Bna28Zqj84Ccg8Ozhe3gf9gww&oe=68163B35",
        "https://scontent.fbkk12-2.fna.fbcdn.net/v/t1.15752-9/488507485_4017514141828607_4772317503505231731_n.jpg?_nc_cat=104&ccb=1-7&_nc_sid=0024fc&_nc_ohc=NnI61VLot0AQ7kNvwEMfSkY&_nc_oc=Adk9ISnqxZCR-A2MCOhyie_eJ9S3zNzh0kEE1KOnnZdy2-hXjJuh_Sr2qE97P_eagIDBFrEc2mC8098OnuU1XVHI&_nc_ad=z-m&_nc_cid=1277&_nc_zt=23&_nc_ht=scontent.fbkk12-2.fna&oh=03_Q7cD1wHrqQlEUmo-ole3HbsJWC4DqKdg7zhmac8jHCmdaw-Y5A&oe=68164A65",
        "https://scontent.fbkk13-2.fna.fbcdn.net/v/t1.15752-9/462638450_1094421315052500_1091732873712148705_n.jpg?_nc_cat=111&ccb=1-7&_nc_sid=0024fc&_nc_ohc=5RLH2Vbuy-kQ7kNvgEY5xC_&_nc_oc=Adk2L6WcDmhGL-amyAQjrWXMJ86rReUqIO5-mgKbEV-VA8COpps3bJryjFjlcG-LbtWZl6XAI60-W06VHrh8morI&_nc_ad=z-m&_nc_cid=1277&_nc_zt=23&_nc_ht=scontent.fbkk13-2.fna&oh=03_Q7cD1wGSraolsg6eZE978iWuAX8S-yRWBE99M34nMgUFbViINg&oe=68162A71",
      ],
      currentImageIndex: 0,
      artworks: [
        {
          title: "Raiden Shogun",
          description: "งาน Fanart ตัวละครจากเกม Genshin Impact",
          date: "January 2024",
          imageUrl: "https://scontent.fbkk8-2.fna.fbcdn.net/v/t1.15752-9/486751318_2462413700778042_8695487125184835889_n.jpg?stp=dst-jpg_s480x480_tt6&_nc_cat=110&ccb=1-7&_nc_sid=0024fc&_nc_ohc=msAXfluqUF4Q7kNvwFhWxLN&_nc_oc=AdnPqoIFILx44REQz8baliYcc5uB_vgKdI_nuga_8FcP9m5IDVjahJHlCj9Ni9Wp8LaWspYcvgLlkS-RzVJqpYd8&_nc_ad=z-m&_nc_cid=1277&_nc_zt=23&_nc_ht=scontent.fbkk8-2.fna&oh=03_Q7cD1wH9MWZlqCkxAw8HYBFjeO9jvmG4ZSFD5-j_Bt3wqDeUKg&oe=68161933"
        },
        {
          title: "Ivan-Alien Stage",
          description: "งาน Fanart จากอนิเมชั่นเพลงช่อง YT:VIVNOS",
          date: "January 2025 ",
          imageUrl: "https://scontent.fbkk12-1.fna.fbcdn.net/v/t1.15752-9/489332948_522795737297384_2189127912237761313_n.png?_nc_cat=106&ccb=1-7&_nc_sid=0024fc&_nc_ohc=5Qqj1E8WeyMQ7kNvgH5sohs&_nc_oc=AdkNFYqBNDFzAHqjznQz02SNPIcm08G7VCSFJ72upy7cmvg_ZNCSUWjJwMvqDsSq-ZTdQunzHXSuyasKUU1FTVLo&_nc_ad=z-m&_nc_cid=1277&_nc_zt=23&_nc_ht=scontent.fbkk12-1.fna&oh=03_Q7cD1wERYw88wG9qM3NoFMptdM2-nSNcjHeobpgH2k2lVfmONQ&oe=68162A9D"
        },
        {
          title: "Yoshida",
          description: "งาน Fanart ตัวละครจากอนิเมะเรื่อง Chainsaw Man",
          date: "May 2024",
          imageUrl: "https://scontent.fbkk12-4.fna.fbcdn.net/v/t1.15752-9/486835538_1310943876656050_2727292941768839342_n.jpg?_nc_cat=103&ccb=1-7&_nc_sid=0024fc&_nc_ohc=JniZ_Qjly4MQ7kNvgE8wpYh&_nc_oc=AdkFBMQxooZmeYsQg36DOKdM_4AZZWUnU8HMFWFvxZIfhDossZr1rCr1-ycnduBQGCmk53JNbYwQIufZUIfSSa87&_nc_ad=z-m&_nc_cid=1277&_nc_zt=23&_nc_ht=scontent.fbkk12-4.fna&oh=03_Q7cD1wE2fqZblwqAzWcuJivLj1QyxsqSRPKDe6d_iZnKyqKysA&oe=68163F86"
        },
        {
          title: "Commission",
          description: "จะเป็นงานที่ลูกค้าจ้างวาดค่ะ ขออนุญาติลูกค้าแล้วนะคะ",
          date: "July 2024",
          imageUrl: "https://scontent.fbkk8-2.fna.fbcdn.net/v/t1.15752-9/486493441_1036830825000817_7648089839402340855_n.jpg?_nc_cat=110&ccb=1-7&_nc_sid=0024fc&_nc_aid=0&_nc_ohc=ztx8--t8Q3IQ7kNvgHxcnRR&_nc_oc=AdkyG14y3_tC50daDfbR8v4szQvmvsroqMBB0ni4Lem1v8pdE9uJfX6QQAKnMEHSfSyR7EGCznw1urBKQFma_Utu&_nc_ad=z-m&_nc_cid=1277&_nc_zt=23&_nc_ht=scontent.fbkk8-2.fna&oh=03_Q7cD1wFfr2nePtpT2PKlvcXAHGIca_mgQOhpOt2zLp0m8xV74Q&oe=68163FC6"
        },
        {
          title: "My Original Character",
          description: "เป็น Original Character",
          date: "September 2024",
          imageUrl: "https://scontent.fbkk12-1.fna.fbcdn.net/v/t1.15752-9/482781467_1454713265498875_2815380714215839924_n.jpg?_nc_cat=106&ccb=1-7&_nc_sid=0024fc&_nc_ohc=4swB0yl6aMsQ7kNvgGWxOvf&_nc_oc=AdnICziAQaqkqlFe9rGyTXl4GDzeZVJ8zq6oBDBLTpm7szHZyFX3JD1V_kDVDFdcnXcq9iP8mE-G66OQVpZsRWZ5&_nc_ad=z-m&_nc_cid=1277&_nc_zt=23&_nc_ht=scontent.fbkk12-1.fna&oh=03_Q7cD1wGg09D4KVTOMrgnwK9lOT4s7olJLJfniVDHRStdiTGulA&oe=68162C9A"
        },
        {
          title: "The Herta",
          description: "งาน Fanart ตัวละครจากเกม Honkai Star Rail",
          date: "November 2024",
          imageUrl: "https://scontent.fbkk13-3.fna.fbcdn.net/v/t1.15752-9/488074746_992528369694023_1694180447193060752_n.jpg?_nc_cat=108&ccb=1-7&_nc_sid=0024fc&_nc_ohc=qNLR-R4ns5IQ7kNvgHMX5bh&_nc_oc=AdnEOCz2iNOsus4whOYwlrLsPFBF3asrMMkI1NJnD9QVthcesywCvTKkiX64mF1Nvobo118VU_ltoRPRNACe2zQv&_nc_ad=z-m&_nc_cid=1277&_nc_zt=23&_nc_ht=scontent.fbkk13-3.fna&oh=03_Q7cD1wG-GA3R3-L5GkO2A0uh4zZSHSNwBAewT4OnYoh7ikIkuA&oe=6816392E"
        },
        {
          title: "Aventurine",
          description: "งาน Fanart ตัวละครจากเกม Honkai Star Rail",
          date: "February 2024",
          imageUrl: "https://scontent.fbkk13-3.fna.fbcdn.net/v/t1.15752-9/487700053_693604029856433_3611638314244090445_n.jpg?_nc_cat=108&ccb=1-7&_nc_sid=0024fc&_nc_ohc=vHtmSc-Rt0AQ7kNvgEY8T8J&_nc_oc=AdnnAyZveRA-rklQJ1xvDjAbzg-K6D5HL-bCguGlwdHIZthRGF_rD4VCCPErhZVWSGNOJ1VZAkaQh6wTU1QuCKtc&_nc_ad=z-m&_nc_cid=1277&_nc_zt=23&_nc_ht=scontent.fbkk13-3.fna&oh=03_Q7cD1wEp7UgUVOTUV3bayWaONpn0oVUz0SG_QogyuJfEjo3kbQ&oe=6816338F"
        },
        {
          title: "Commission",
          description: "จะเป็นงานที่ลูกค้าจ้างวาดค่ะ ขออนุญาติลูกค้าแล้วนะคะ",
          date: "April 2024",
          imageUrl: "https://scontent.fbkk13-3.fna.fbcdn.net/v/t1.15752-9/488596790_2173546123099156_4895059707815276921_n.jpg?_nc_cat=108&ccb=1-7&_nc_sid=0024fc&_nc_ohc=kztKY_QUGcEQ7kNvgFHvWJE&_nc_oc=AdklrCPu8pa6oCyqxEulmb7HI1xZVXw9gWdzwH7yhqFTYOlD27kd4E2p3EQEo0g_CxYL_qQxOKrN5pumHHvfCsOh&_nc_ad=z-m&_nc_cid=1277&_nc_zt=23&_nc_ht=scontent.fbkk13-3.fna&oh=03_Q7cD1wE2kFgUigGTJwhD8NWwAuCeMGiDvN5PxIepTO95SYM0jA&oe=68161F47"
        },
        {
          title: "Commission",
          description: "จะเป็นงานที่ลูกค้าจ้างวาดค่ะ ขออนุญาติลูกค้าแล้วนะคะ",
          imageUrl: "https://scontent.fbkk12-2.fna.fbcdn.net/v/t1.15752-9/487993830_1126089742864645_5739698736256336790_n.jpg?_nc_cat=104&ccb=1-7&_nc_sid=0024fc&_nc_ohc=Db7GNSO71zIQ7kNvgFG8ngK&_nc_oc=AdkaDsd17P8UbVNj0rSbWRgqbgp3lge-zYVtjvd4EqozaAasQw-LomFq5AQZm1LZaWBtVtONFE4piuSEY4e5Dv_P&_nc_ad=z-m&_nc_cid=1277&_nc_zt=23&_nc_ht=scontent.fbkk12-2.fna&oh=03_Q7cD1wEb0yBwtLgvINdNzFAvGCzN3NgOzl9ae4ShicrmcyZX5A&oe=681623CC"
        },
        {
          title: "Kazuha",
          description: "งาน Fanart ตัวละครจากเกม Genshin Impact",
          date: "August 2024",
          imageUrl: "https://scontent.fbkk13-1.fna.fbcdn.net/v/t1.15752-9/486468238_1235998447891587_5531220101707909871_n.jpg?_nc_cat=105&ccb=1-7&_nc_sid=0024fc&_nc_ohc=mV-OKchVzbwQ7kNvgF-GGqa&_nc_oc=Adn0k9HUoo8cJsde7Q2N1iZ0K9-SqJFNdwBPaNQ3NU_rRjyRJs8tpYIPBvE0IS-dPZLCLGfNLJbbi2QVwy1KycR0&_nc_ad=z-m&_nc_cid=1277&_nc_zt=23&_nc_ht=scontent.fbkk13-1.fna&oh=03_Q7cD1wHlD8ewL4JVdxVNwGWFgLyq0FlFVjRPplwy7l2vnOcO7g&oe=68163363"
        },
        {
          title: "Commission",
          description: "จะเป็นงานที่ลูกค้าจ้างวาดค่ะ ขออนุญาติลูกค้าแล้วนะคะ",
          date: "August 2024",
          imageUrl: "https://scontent.fbkk12-1.fna.fbcdn.net/v/t1.15752-9/486676988_3881104955477441_3545438398445894569_n.jpg?_nc_cat=106&ccb=1-7&_nc_sid=0024fc&_nc_ohc=WixnHATkRYMQ7kNvgEkyPOL&_nc_oc=AdkYdBIYlhkSFLbSozU79gH5RXx8mCAsyL75XW8LUDZf8hCArNQDSvr6esK_g8Ca5RLVLs_k_176bwZgCtkiMj0m&_nc_ad=z-m&_nc_cid=1277&_nc_zt=23&_nc_ht=scontent.fbkk12-1.fna&oh=03_Q7cD1wE72OFdWNL3XZwMVSx9o32JdSIBev38JKr9OxJqgwEsYA&oe=68162C50"
        },
        {
          title: "Scaramoucha",
          description: "งาน Fanart ตัวละครจากเกม Genshin Impact",
          date: "August 2024",
          imageUrl: "https://scontent.fbkk13-2.fna.fbcdn.net/v/t1.15752-9/486552640_2788704514850856_4184116060126156836_n.jpg?_nc_cat=111&ccb=1-7&_nc_sid=0024fc&_nc_ohc=IgcmlZLU21EQ7kNvgFqKL89&_nc_oc=AdkzeJcurcYSJz9_-RnfDp_fOAeqRyp1C3E1rNrMIsiqYNtwOCMkziZFY48W02e6RNQZG4RHbZ0FG5xyCcEH2Di5&_nc_ad=z-m&_nc_cid=1277&_nc_zt=23&_nc_ht=scontent.fbkk13-2.fna&oh=03_Q7cD1wF-6fWUAZNgCI7Vk-UDHo9hS-09kaJAiwJxxBb2hZtXIQ&oe=6816449D"
        }
      ]
    };
  },
  methods: {
    nextImage() {
      if (this.currentImageIndex < this.images.length - 1) {
        this.currentImageIndex++;
      }
    },
    prevImage() {
      if (this.currentImageIndex > 0) {
        this.currentImageIndex--;
      }
    },
    updateStudent() {
      alert("ข้อมูลนิสิตถูกบันทึกแล้ว");
      this.isEditing = false;
    },
    cancelEdit() {
      this.isEditing = false;
    },
    startCourseEdit(index = null) {
      this.isEditingCourse = true;
      if (index !== null) {
        this.editCourseIndex = index;
        const course = this.courses[index];
        this.courseForm = { ...course };
      } else {
        this.editCourseIndex = null;
        this.courseForm = { code: "", name: "", credit: "", grade: "" };
      }
    },
    submitCourseForm() {
      if (this.editCourseIndex !== null) {
        this.courses[this.editCourseIndex] = { ...this.courseForm };
      } else {
        this.courses.push({ ...this.courseForm });
      }
      this.cancelCourseEdit();
    },
    cancelCourseEdit() {
      this.isEditingCourse = false;
      this.editCourseIndex = null;
    },
    deleteCourse(index) {
      this.courses.splice(index, 1);
    },
    editCourse(index) {
      this.startCourseEdit(index);
    },
    openModal(imageUrl) {
      this.modalImageUrl = imageUrl;
      this.showModal = true;
      document.body.style.overflow = 'hidden';
    },
    closeModal() {
      this.showModal = false;
      document.body.style.overflow = 'auto';
    }
  }
};
</script>

<style>
#app {
  font-family: Arial, Helvetica, sans-serif, Helvetica, Arial, sans-serif;
  text-align: center;
  color: #000000;
  margin-top: 50px;
  background-color: #191928;
}

h1 {
  color: #000000;
  font-size: 50px;
  margin-bottom: 50px;
}

.tabs {
  display: flex;
  justify-content: center;
  margin-bottom: 30px;
}

.tabs button {
  padding: 10px 20px;
  cursor: pointer;
  background-color: #2e2d2d;
  border: 1px solid #0c0c0c;
  margin: 0 10px;
  border-radius: 5px;
  font-size: 18px;
}

.tabs button.active {
  background-color: #e6b2dc;
  color: rgb(247, 96, 240);
}

.student-info,
.courses-info,
.image-gallery,
.art-gallery {
  background-color: #eac6d9;
  padding: 40px;
  margin-top: 50px;
  border-radius: 20px;
  box-shadow: 0 10px 120px rgba(104, 65, 65, 0.1);
  max-width: 800px;
  width: 1000%;
  margin-left: auto;
  margin-right: auto;
}

.student-image {
  margin-right: 40px;
}

.student-image img {
  border-radius: 100%;
  width: 300px;
  height: 300px;
}

.student-details {
  text-align: left;
  font-size: 18px;
}

.edit-button-container {
  margin-top: 20px;
  text-align: center;
}

form {
  display: flex;
  flex-direction: column;
  max-width: 500px;
  margin: 20px auto;
  background-color: #1d0513;
  padding: 30px;
  border-radius: 10px;
  box-shadow: 5px 10px 10px rgba(0, 0, 0, 0.1);
}

input {
  padding: 10px;
  margin-top: 10px;
  width: 100%;
  border-radius: 4px;
  border: 1px solid #a04040;
  font-size: 16px;
}

button {
  padding: 10px;
  margin-top: 10px;
  cursor: pointer;
  border: none;
  border-radius: 4px;
}

button[type="button"] {
  background-color: #f44336;
  color: rgb(252, 255, 253);
}

button[type="submit"] {
  background-color: #4caf50;
  color: white;
}

button:hover {
  opacity: 0.8;
}

.course-item {
  margin-bottom: 20px;
}

.course-row {
  display: flex;
  justify-content: space-between;
  font-size: 18px;
}

.course-actions {
  margin-top: 20px;
  text-align: right;
}

.gallery {
  display: flex;
  justify-content: center;
  margin-bottom: 20px;
}

.image-item img {
  width:450px;
  height:450px;
  border-radius: 20px;
}

.navigation-buttons {
  display: flex;
  justify-content: center;
  gap: 20px;
}

button {
  padding: 20px 20px;
  font-size: 16px;
}


.art-gallery {
  max-width: 1200px;
}

.art-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
  gap: 30px;
  margin-top: 30px;
}

.art-item {
  background: white;
  border-radius: 10px;
  overflow: hidden;
  box-shadow: 0 5px 15px rgba(0,0,0,0.1);
  transition: transform 0.3s ease;
}

.art-item:hover {
  transform: translateY(-5px);
}

.art-item img {
  width: 100%;
  height: 250px;
  object-fit: cover;
  cursor: pointer;
  transition: transform 0.3s;
}

.art-item img:hover {
  transform: scale(1.02);
}

.art-info {
  padding: 20px;
  text-align: left;
}

.art-info h3 {
  margin-top: 0;
  color: #333;
}

.art-info p {
  color: #2f0529;
  margin-bottom: 10px;
}

.art-date {
  color: #130216;
  font-size: 0.9em;
  font-style: italic;
}


.view-full-btn {
  background-color: #3498db;
  color: white;
  padding: 8px 16px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  margin-top: 10px;
  font-size: 14px;
  transition: background-color 0.3s;
}

.view-full-btn:hover {
  background-color: #2980b9;
}


.modal {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: rgba(0, 0, 0, 0.8);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
}

.modal-content {
  position: relative;
  max-width: 90%;
  max-height: 90%;
}

.modal-image {
  max-width: 100%;
  max-height: 80vh;
  display: block;
  margin: 0 auto;
  border-radius: 8px;
  box-shadow: 0 5px 30px rgba(0, 0, 0, 0.3);
}

.close-btn {
  position: absolute;
  top: -40px;
  right: 0;
  color: rgb(227, 218, 218);
  font-size: 30px;
  cursor: pointer;
  background: rgba(0, 0, 0, 0.5);
  width: 40px;
  height: 40px;
  border-radius: 50%;
  display: flex;
  justify-content: center;
  align-items: center;
  transition: background-color 0.3s;
}

.close-btn:hover {
  background: rgba(0, 0, 0, 0.8);
}
</style>