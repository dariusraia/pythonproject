               import cv2 , time

          first_frame = None
          video = cv2.VideoCapture(0)   #Creeaza o captura video pentru a inregistra.
                                        #(0)--camera web incorporata

          while True:



              '''check=variabila de tip boolean, returneaza adevarat daca este
              posibila citirea obiectului VideoCapture'''
              '''frame=matice a pachetului NumPy si reprezinta prima imagine facuta de captura video'''



              check, frame = video.read()
              gray = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)      #transforma imaginea int-o nuanta de gri pentru evidentierea obiectului
              gray = cv2.GaussianBlur(gray, (21, 21), 0)          #transforma nuanta de gri Blur-ul Gaussian



              if first_frame is None :           #structura repetitiva este folosesta la stocarea primei imagine din video
                  first_frame=gray               #prima imagine va fi stocata in variabia first_frame
                  continue


                  '''daca variabila first_frame are valoarea 0, adica none,va continua sa astepte pana cand primul 
                  obiect va aparea '''


              delta_frame = cv2.absdiff(first_frame, gray)     #calculeaza diferenta intre prima imagine si cele care vor urma dupa aceasta

              thresh_delta = cv2.threshold(delta_frame, 30, 255, cv2.THRESH_BINARY)[1]
              thresh_delta = cv2.dilate(thresh_delta, None, iterations=0)

              '''instructiunie de sus fac rost de variabila threshold care transforma pixeli in negru daca acestia sunt mai mici de 30
              respectiv in alb daca sunt mai mari de 30,astfel orice obict ar aparea va fi convertit in alb
              fiind mai usor de observat'''


              contours, hierarchy = cv2.findContours(thresh_delta.copy(), cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE)[-2:]

              #defineste conturul si adauga cadran de incadrare pentru obiecte

              '''Elimina zgomotul si umbrele ,pastreaza doar partea alba a carui are e mai mare
                         de 1000 pixeli'''

              for contour in contours:
                  if cv2.contourArea(contour)<1000:
                      continue

                  (x,y,w,h)=cv2.boundingRect(contour)
                  cv2.rectangle(frame, (x, y), (x + w, y + h), (0, 255, 0), 3)



              cv2.imshow('frame', frame)
              cv2.imshow('Captureing',gray)
              cv2.imshow('delta', delta_frame)                  #afisarea celor 4 ferestre
              cv2.imshow('thresh', thresh_delta)


              key=cv2.waitKey(1)            #imaginea se va schimba dupa 1 ms printr-o variabila key


              if key == ord('q'):           #va iesi din loop odata ce utilizatorul va apasa tasta'q'
                  break

          video.release()                   # va inchide inchide camera
          cv2.destroyAllWindows             #va inchide ferestrele deschise







